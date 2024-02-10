OSCP Enumeration
========================================

<https://github.com/oncybersec/oscp-enumeration-cheat-sheet/blob/main/README.md>

![scribble](../media/Teste2.png)


OSCP Enumeration Cheat Sheet
A collection of commands and tools used for conducting enumeration during my OSCP journey.

Description
This is an enumeration cheat sheet that I created while pursuing the OSCP. It also includes the commands that I used on platforms such as Vulnhub and Hack the Box. Some of these commands are based on those executed by the Autorecon tool.

Disclaimer
This cheat sheet should not be considered to be complete and only represents a snapshot in time when I used these commands for performing enumeration during my OSCP journey. These commands should only be used for educational purposes or authorised testing.

Table of Contents

- [Host Discovery](OSCP%20Enumeration.md#HostDiscovery) 
- [Port scan](OSCP%20Enumeration.md#PortScan)
- [Proxychains](OSCP%20Enumeration.md#Proxychains)
- [Autorecon](OSCP%20Enumeration.md#Autorecon)
- [Services](OSCP%20Enumeration.md#Services)
    - [FTP (21/tcp)](OSCP%20Enumeration.md#FTP)
    - [SSH (22/tcp)](OSCP%20Enumeration.md#SSH)
    - [SMTP (25/tcp)](OSCP%20Enumeration.md#SMTP)
    - [DNS (53/tcp, 53/udp)](OSCP%20Enumeration.md#DNS)
    - [HTTP/HTTPS (80/tcp, 443/tcp)](OSCP%20Enumeration.md#HTTP)
    - [Kerberos (88/tcp, 464/tcp)](OSCP%20Enumeration.md#Kerberos)
    - [POP3/POP3S (110/tcp, 995/tcp)](OSCP%20Enumeration.md#POP)
    - [RPC (111/tcp, 135/tcp)](OSCP%20Enumeration.md#RPC)
    - [ident (113/tcp)](OSCP%20Enumeration.md#ident)
    - [NTP (123/udp)](OSCP%20Enumeration.md#NTP)
    - [NetBIOS-NS (137/udp)](OSCP%20Enumeration.md#NetBIOS-NS)
    - [SMB (139/tcp, 445/tcp)](OSCP%20Enumeration.md#SMB)
    - [IMAP/IMAPS (143/tcp, 993/tcp)](OSCP%20Enumeration.md#IMAP)
    - [SNMP (161/udp)](OSCP%20Enumeration.md#SNMP)
    - [LDAP (389/tcp, 3268/tcp, 636/tcp)](OSCP%20Enumeration.md#LDAP)
    - Java RMI (1100/tcp)
    - [MSSQL (1433/tcp)](OSCP%20Enumeration.md#MSSQL)
    - [Oracle TNS listener (1521/tcp)](OSCP%20Enumeration.md#OracleTNSListener)
NFS (2049/tcp)
MySQL (3306/tcp)
RDP (3389/tcp)
SIP (5060/udp)
PostgreSQL (5432/tcp)
VNC (5900/tcp)
AJP (8009/tcp)
Active Directory
Enumeration
Host

## HostDiscovery

### fping

    fping -a -q -g 10.0.0.0/24  -> pingar uma vez cada host da rede especificada com a opção -g
    
    fping -q -g 10.0.0.0/24 -a -> só mostra os hosts ativos --alive e -q de quiet
    
    fping -d -g 10.0.0.0/24 -> reverse DNS lookup 
    
    fping -i 1 -g 10.0.0.1/24 -> fastest ping scan
    
### Bettercap 

    bettercap -eval "net.probe on; sleep 3;q"

formatting the response

	bettercap -eval "net.probe on; sleep 3;q" | cut -d' ' -f 4 | sed ':a;N;$! ba;s/\n/,/g'


### NMAP

    nmap -sn -n 10.11.1.0/24

## PortScan

Full TCP port scan
    
    sudo nmap -Pn -p- -oN alltcp_ports.txt <hostname>

Full TCP port scan (safe scripts + version detection)
    
    sudo nmap -Pn -sC -sV -p- -oN alltcp.txt <hostname>

Top 20 UDP port scan

    sudo nmap -Pn -sU -sV -sC --top-ports=20 -oN top_20_udp_nmap.txt <hostname>

    nmap -sU -p 53,161,111,137,139,500,2049 -Pn 10.11.1.0/24

## Proxychains

Top 20 TCP port scan

    proxychains nmap -Pn -sT --top-ports=20 --open -oN top_20_tcp_nmap.txt <hostname>

Top 1000 TCP port scan

    proxychains nmap -Pn -sT --top-ports=1000 --open -oN top_1000_tcp_nmap.txt <hostname>

Scan TCP ports (safe scripts + version detection)

    proxychains nmap -Pn -sT -sC -sV -p 21,22,80 -oN tcp_nmap_sC_sV.txt <hostname>

## Autorecon
Scan single target
sudo autorecon -o enumeration <hostname>

Scan multiple targets

    sudo autorecon -o enumeration <hostname>1 <hostname>2 <hostname>3 <hostname>4
  
## Services

### FTP

FTP (21/tcp)

 Version detection + NSE scripts

    nmap -Pn -sV -p 21 --script="banner,(ftp* or ssl*) and not (brute or broadcast or dos or external or fuzzer)" -oN "tcp_21_ftp_nmap.txt" <hostname>

### SSH

SSH (22/tcp)

Version detection + NSE scripts

    nmap -Pn -sV -p 22 --script=banner,ssh2-enum-algos,ssh-hostkey,ssh-auth-methods -oN tcp_22_ssh_nmap.txt <hostname>

### SMTP

SMTP (25/tcp)

Version detection + NSE scripts

    nmap -Pn -sV -p 25 "--script=banner,(smtp* or ssl*) and not (brute or broadcast or dos or external or fuzzer)" -oN tcp_25_smtp_nmap.txt <hostname>

smtp-user-enum

    /home/kali/.local/bin/smtp-user-enum -V -m RCPT -w -f '<user@example.com>' -d 'domain.local' -U "/usr/share/metasploit-framework/data/wordlists/unix_users.txt" <hostname> 25 2>&1 | tee "tcp_25_smtp_user-enum.txt"


    VRFY root
    VRFY <username>
    
    user <username>
    pass <password>
    list
    
    retr 1
    retr 2

### DNS

DNS (53/tcp, 53/udp)

Version detection + NSE scripts

    sudo nmap -Pn -sU -sV -p 53 "--script=banner,(dns* or ssl*) and not (brute or broadcast or dos or external or fuzzer)" -oN udp_53_dns_nmap.txt <hostname>

Perform zone transfer (only works over port 53/tcp)
Vale considerar a entradea de DNS no arquivo resolv.conf:

    echo "nameserver <ip_dns_server>" >> /etc/resolv.conf

    dig axfr @<hostname> $domain 2>&1 | tee "tcp_53_dns_dig.txt"

    host -t axfr <hostname> $dns_server
    
    dnsenum htb.local
    
    dnsrecon -d _msdcs.thinc.local -D /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -t brt

Perform reverse DNS lookup (may display NS record containing domain name)

    nslookup <hostname> <hostname>

Brute force subdomains
   
    gobuster dns -d $domain -w /usr/share/seclists/Discovery/DNS/bitquark-subdomains-top100000.txt -t 16 -o "tcp_53_dns_gobuster.txt"

### HTTP/HTTPS

HTTP/HTTPS (80/tcp, 443/tcp)

Procurar por comentários nas páginas para verificar alguma informação sensível ou credencial

Criar wordlists com cewl se houver usuários ou possíveis senhas nas páginas:

#### Cewl

    cewl $url/index.php -m 3 --with-numbers -w cewl.txt
    

Version detection + NSE scripts

    nmap -Pn -sV -p $port "--script=banner,(http* or ssl*) and not (brute or broadcast or dos or external or http-slowloris* or fuzzer)" -oN tcp_port_protocol_nmap.txt <hostname>

Nikto

    nikto -h $url 2>&1 | tee "tcp_port_protocol_nikto.txt"

Directory brute force (Tentar várias listas de diretório possíveis dentro do diretório:)

/usr/share/seclists/Discovery/Web-Content/

    gobuster dir -u $url -w /usr/share/seclists/Discovery/Web-Content/common.txt -x "txt,html,php,asp,aspx,jsp" -s "200,204,301,302,307,403,500" -k -t 16 -o "tcp_port_protocol_gobuster.txt"  
    
    gobuster dir -u http://10.10.10.63:50000 -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -x "txt,html,php,asp,aspx,jsp" -k -t 16 -o "tcp_port_protocol_jeeves_gobuster.txt"

    gobuster dir -u http://10.10.10.63:50000 -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -x "txt,html,php,asp,aspx,jsp" -k -t 16 -o "tcp_port_protocol_jeeves_gobuster.txt" -c <cookie>

    python3 /opt/dirsearch/dirsearch.py -u $url -t 16 -e txt,html,php,asp,aspx,jsp -f -x 403 -w /usr/share/seclists/Discovery/Web-Content/common.txt --plain-text-report="tcp_port_protocol_dirsearch.txt"

Dirbuster (GUI): only perform extension brute force - disable 'Brute Force Dirs'

    wfuzz -c -z file,/usr/share/seclists/Discovery/Web-Content/common.txt --hc 404 -t 16 $url/FUZZ 2>&1 | tee "tcp_port_http_wfuzz.txt"

 Directory brute force recursively with max depth = 2
    
    python3 /opt/dirsearch/dirsearch.py -u $url/apps/ -t 16 -e txt,html,php -f -x 403 -r -R 2 -w /usr/share/seclists/Discovery/Web-Content/common.txt --plain-text-report="tcp_port_protocol_dirsearch_apps.txt"

Whatweb

    whatweb --color=never --no-errors -a 3 -v $url 2>&1 | tee "tcp_port_protocol_whatweb.txt"
Wordpress

Enumerate vulnerable plugins and themes, timthumbs, wp-config.php backups, database exports, usernames and media IDs

    wpscan --url http://<hostname> --no-update --disable-tls-checks -e vp,vt,tt,cb,dbe,u,m --plugins-detection aggressive --plugins-version-detection aggressive -f cli-no-color 2>&1 | tee tcp_port_protocol_wpscan.txt

Enumerate all plugins

    wpscan --url $url --disable-tls-checks --no-update -e ap --plugins-detection aggressive -f cli-no-color 2>&1 | tee tcp_port_protocol_wpscan_plugins.txt

#### Subdomains

    wfuzz -u https://streamio.htb -H "Host: FUZZ.streamio.htb" -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt --hh 315

#### Robots.txt
 
/robots.txt
Only get HTTP headers

 sitemap.xml

curl -I $url


Drupal

    python3 drupwn --version 7.28 --mode enum --target $url

    droopescan scan drupal -u $url

#### Shellshock

Check if bash vulnerable to CVE-2014-6271 (bash vulnerable if ‘vulnerable’ in output)

    env x='() { :;}; echo vulnerable' bash -c "echo this is a test" 

#### Brute force CGI files

    gobuster dir -u http://<hostname> -w /usr/share/seclists/Discovery/Web-Content/common.txt -x "txt,html,php,asp,aspx,jsp" -k -t 16 -o "tcp_port_protocol_gobuster.txt"

    wfuzz -c -z file,/usr/share/seclists/Discovery/Web-Content/CGIs.txt --hc 404 -t 16 $url/cgi-bin/FUZZ 2>&1 | tee "tcp_port_protocol_wfuzz.txt"

Webmin uses cgi files - versions up to 1.700 vulnerable to shellshock (http://www.webmin.com/security.html)

 Feito isso, temos de verificar algumas coisas:

 Enumerar admin console e tentar brutar gerando uma wordlist com cewl ou tentar com rockyou.txt
 Navegar na aplicacao, obter a versao da aplicacao que esta sendo executada e buscar por exploits no google ou searchsploit. 
 OBS: Vale considerar que nao e somente a versao do framework, linguagem de desenvolvimento ou a versao do web server, mas sim pelo proprio nome da aplicacao como no caso do phreebooks e apphp microblog, por exemplo

Outra experiência que vivi é que achei um exploit que descobria conteúdo de outros arquivos dentro do servidor. Não procurar somente por exploits de RCE, mas também que faça leituras de arquivos sensíveis na máquina alvo nos permite fazer a leitura de arquivos cujo conteúdo são as senhas dos usuários como /etc/passwd e /etc/shadow e depois crackear os hashes de senha offline.

### Kerberos

Kerberos (88/tcp, 464/tcp)

Version detection + NSE scripts

    nmap -Pn -sV -p $port --script="banner,krb5-enum-users" -oN "tcp_port_kerberos_nmap.txt" <hostname>

### POP3/POP3S

POP3/POP3S (110/tcp, 995/tcp)

Version detection + NSE scripts

    nmap -Pn -sV -p $port "--script=banner,(pop3* or ssl*) and not (brute or broadcast or dos or external or fuzzer)" -oN tcp_port_pop3_nmap.txt <hostname>


### RPC

RPC (111/tcp, 135/tcp)
msrpc/rpcbind

Version detection + NSE scripts

    nmap -Pn -sV -p $port --script=banner,msrpc-enum,rpc-grind,rpcinfo -oN tcp_port_rpc_nmap.txt <hostname>  

rpcinfo

List all registered RPC programs

    rpcinfo -p <hostname>    

Provide compact results

    rpcinfo -s <hostname>

Null session

    rpcclient -U "" -N <hostname>
    srvinfo
    enumdomusers
    getdompwinfo
    querydominfo
    netshareenum
    netshareenumall

### ident

ident (113/tcp)

Enumerate users services running as

    ident-user-enum <hostname> 22 25 80 445


### NTP

NTP (123/udp)

Run ntp-info NSE script

    sudo nmap -sU -p 123 --script ntp-info <hostname>

### NetBIOS-NS

NetBIOS-NS (137/udp)

enum4linux

    enum4linux -a -M -l -d <hostname> 2>&1 | tee "enum4linux.txt"

nbtscan

    nbtscan -rvh <hostname> 2>&1 | tee "nbtscan.txt"

### SMB

SMB (139/tcp, 445/tcp)

Version detection + NSE scripts

    nmap -Pn -sV -p 445 "--script=banner,(nbstat or smb* or ssl*) and not (brute or broadcast or dos or external or fuzzer)" --script-args=unsafe=1 -oN tcp_445_smb_nmap.txt <hostname>

smbmap

List share permissions

    smbmap -H <hostname> -P 445 2>&1 | tee -a "smbmap-share-permissions.txt"; smbmap -u null -p "" -H <hostname> -P 445 2>&1 | tee -a "smbmap-share-permissions.txt"

List share contents

    smbmap -H <hostname> -P 445 -R 2>&1 | tee -a "smbmap-list-contents.txt"; smbmap -u null -p "" -H <hostname> -P 445 -R 2>&1 | tee -a "smbmap-list-contents.txt"

enum4linux

    enum4linux -a -M -l -d <hostname> 2>&1 | tee "enum4linux.txt"

Enumerate Samba version (*nix)

NB: change interface tcpdump listening on

    sudo ./smbver.sh <hostname> 139

Null session

    smbmap -H <hostname>
    smbclient -L //<hostname>

Enumerate shares

    nmap --script smb-enum-shares -p 445 <hostname>

Connect to wwwroot share (try blank password)

    smbclient \\\\<hostname>\\wwwroot

Nmap scans for SMB vulnerabilities (NB: can cause DoS)

RRAS Service Overflow
<https://docs.microsoft.com/en-us/security-updates/securitybulletins/2006/ms06-025>

    nmap -Pn -sV -p 445 --script="smb-vuln-ms06-025" --script-args="unsafe=1" -oN "tcp_445_smb_ms06-025.txt" <hostname>

DNS RPC Service Overflow
,https://docs.microsoft.com/en-us/security-updates/securitybulletins/2007/ms07-029>

    nmap -Pn -sV -p 445 --script="smb-vuln-ms07-029" --script-args="unsafe=1" -oN "tcp_445_smb_ms07-029.txt" <hostname>

Server Service Vulnerability
<https://docs.microsoft.com/en-us/security-updates/securitybulletins/2008/ms08-067>

    nmap -Pn -sV -p 445 --script="smb-vuln-ms08-067" --script-args="unsafe=1" -oN "tcp_445_smb_ms08-067.txt" <hostname>

Eternalblue
<https://docs.microsoft.com/en-us/security-updates/securitybulletins/2017/ms17-010>

    nmap -p 445 --script smb-vuln-ms17-010 -oN "tcp_445_smb_ms08-067.txt" <hostname>

### IMAP/IMAPS

IMAP/IMAPS (143/tcp, 993/tcp)

Version detection + NSE scripts

    nmap -Pn -sV -p $port "--script=banner,(imap* or ssl*) and not (brute or broadcast or dos or external or fuzzer)" -oN tcp_port_imap_nmap.txt <hostname>  

### SNMP

SNMP (161/udp)

Version detection + NSE scripts

    sudo nmap -Pn -sU -sV -p 161 --script="banner,(snmp* or ssl*) and not (brute or broadcast or dos or external or fuzzer)" -oN "udp_161_snmp-nmap.txt" <hostname>       

Brute force community strings

    onesixtyone -c /usr/share/seclists/Discovery/SNMP/common-snmp-community-strings-onesixtyone.txt <hostname> 2>&1 | tee "udp_161_snmp_onesixtyone.txt"      

snmpwalk

Enumerate entire MIB tree

    snmpwalk -c public -v1 -t 10 <hostname>

Enumerate Windows users

    snmpwalk -c public -v1 <hostname> 1.3.6.1.4.1.77.1.2.25

Enumerate running Windows processes

    snmpwalk -c public -v1 <hostname> 1.3.6.1.2.1.25.4.2.1.2

Enumerate open TCP ports
    
    snmpwalk -c public -v1 <hostname> 1.3.6.1.2.1.6.13.1.3

Enumerate installed software

    snmpwalk -c public -v1 <hostname> 1.3.6.1.2.1.25.6.3.1.2

Enumerate SNMP device (places info in readable format)

    snmp-check <hostname> -c public

### LDAP

LDAP (389/tcp, 3268/tcp, 636/tcp)

Version detection + NSE scripts

    nmap -Pn -sV -p $port --script="banner,(ldap* or ssl*) and not (brute or broadcast or dos or external or fuzzer)" -oN "tcp_port_ldap_nmap.txt" <hostname>

    ldapsearch -H ldap:// 10.10.10.193 -x -s base namingcontexts
    #Esse comando acima eh pra saber o nome do dominio
        
    ldapsearch -H ldap://10.10.10.193 -x -b "DC=fabricorp,DC=local"


enum4linux

    enum4linux -a -M -l -d <hostname> 2>&1 | tee "enum4linux.txt"

Para LDAPS

Importar o certificado:

    echo -n | openssl s_client -connect 10.10.11.168:636 | sed -ne '/-----BEGIN CERTIFICATE-----/,/-----END CERTIFICATE-----/p' > ldap.pem
    sudo vi /etc/ldap/ldap.conf
    
    TLS_CACERT    /path/to/ldap.pem
    
![qownnotes-media-qSTWZx](../media/qownnotes-media-qSTWZx.png)

    ldapsearch -H ldaps://dc1.scrm.local -Z -D ksimpson@scrm.local -w ksimpson -b "DC=scrm,DC=local" "(objectClass=user)"


### Java RMI

Java RMI (1100/tcp)

Version detection + NSE scripts

    nmap -Pn -sV -p 1100 --script="banner,rmi-vuln-classloader,rmi-dumpregistry" -oN "tcp_110_rmi_nmap.txt" <hostname>

### MSSQL

MSSQL (1433/tcp)

Version detection + NSE scripts

    nmap -Pn -sV -p 1433 --script="banner,(ms-sql* or ssl*) and not (brute or broadcast or dos or external or fuzzer)" --script-args="mssql.instance-port=1433,mssql.username=sa,mssql.password=sa" -oN "tcp_1433_mssql_nmap.txt" <hostname>

mssqlclient.py

MSSQL shell

    mssqlclient.py -db msdb hostname/sa:password@<hostname>

List databases

    SELECT name FROM master.dbo.sysdatabases

List tables

    SELECT * FROM <database_name>.INFORMATION_SCHEMA.TABLES

List users and password hashes

    SELECT sp.name AS login, sp.type_desc AS login_type, sl.password_hash, sp.create_date, sp.modify_date, CASE WHEN sp.is_disabled = 1 THEN 'Disabled' ELSE 'Enabled' END AS status FROM sys.server_principals sp LEFT JOIN sys.sql_logins sl ON sp.principal_id = sl.principal_id WHERE sp.type NOT IN ('G', 'R') ORDER BY sp.name   

### OracleTNSListener

Oracle TNS listener (1521/tcp)
    
    sudo apt update
    sudo apt install tnscmd10g

    tnscmd10g version -h <hostname>

    tnscmd10g status -h <hostname>

Uma outra opção para exploração do serviço Oracle é o seguinte... Primeiro, precisamos instalar o odat:

<https://github.com/quentinhardy/odat>

    python odat.py -h
    
    python odat.py sidguesser -s 10.129.125.94 -p 1521
    
Wordlist:

    /usr/share/metasploit-framework/data/wordlists/oracle_default_userpass.txt
    sed -i 's/ /\//g' /home/kali/ownCloud/owncloud/Area_de_trabalho/estudos/hack_the_box/silo/oracle_default_userpass.txt
    
    python odat.py passwordguesser -s 10.129.125.94 -p 1521 --accounts-file /home/kali/ownCloud/owncloud/Area_de_trabalho/estudos/hack_the_box/silo/oracle_default_userpass.txt
    
Exploração pode ser encontrada [aqui](Oracle.md)

### NFS

NFS (2049/tcp)

Version detection + NSE scripts

    nmap -Pn -sV -p 111,2049 --script="banner,(rpcinfo or nfs*) and not (brute or broadcast or dos or external or fuzzer)" -oN "tcp_111_2049_nfs_nmap.txt" <hostname>

Show mount information

    showmount -e <hostname>

Mount share

    sudo mount -o rw,vers=2 <hostname>:/home /mnt

'-o nolock' used to disable file locking, needed for older NFS servers

    sudo mount -o nolock <hostname>:/home /mnt/

### MySQL

MySQL (3306/tcp)

Version detection + NSE scripts

    nmap -Pn -sV -p 3306 --script="banner,(mysql* or ssl*) and not (brute or broadcast or dos or external or fuzzer)" -oN "tcp_3306_mysql_nmap.txt" <hostname>

MySQL shell

    mysql --host=<hostname> -u root -p

MySQL system variables

    SHOW VARIABLES;     

Show privileges granted to current user    

    SHOW GRANTS;

Show privileges granted to root user

Replace 'password' field with 'authentication_string' if it does not exist

    SELECT user,password,create_priv,insert_priv,update_priv,alter_priv,delete_priv,drop_priv FROM mysql.user WHERE user = 'root';

Exact privileges

    SELECT grantee, table_schema, privilege_type FROM information_schema.schema_privileges;     

Enumerate file privileges (see here for discussion of file_priv)

    SELECT user FROM mysql.user WHERE file_priv='Y';

### RDP

RDP (3389/tcp)

Version detection + NSE scripts
    
    nmap -Pn -sV -p 3389 --script="banner,(rdp* or ssl*) and not (brute or broadcast or dos or external or fuzzer)" -oN "tcp_3389_rdp_nmap.txt" <hostname>

SIP (5060/udp)
Scans for SIP devices on network

svmap <hostname>
Identifies active extensions on PBX

svwar -m INVITE -e 200-250 <hostname>

### PostgreSQL

PostgreSQL (5432/tcp)

Log into postgres remotely

    PGPASSWORD=postgres psql -h <hostname> -p 5437 -U postgres

List databases

    \list
    SELECT datname FROM pg_database;

Use postgres database

    \c postgres

List tables

    \d

Describe table

    \d table

Check if current user superuser (on = yes, off = no)

    SELECT current_setting ('is_superuser');

Get user roles

    \du+

Check user’s privileges over table (pg_shadow)

    SELECT grantee, privilege_type FROM information_schema.role_table_grants WHERE table_name='pg_shadow';

Read file (/etc/passwd)

    CREATE TABLE demo(t text);
    COPY demo FROM '/etc/passwd';
    SELECT * FROM demo;

Read usernames and password hashes

Postgresql password hash format: md5(secret || username) where || denotes string concatenation (remove md5 before cracking hash)

    SELECT usename, passwd from pg_shadow;

Check if plpgsql enabled

Below result indicates that plpgsql enabled:
lanname | lanacl
#---------+---------
 plpgsql |            

    SELECT lanname,lanacl FROM pg_language WHERE lanname = 'plpgsql'

PostgreSQL config file location

    SHOW config_file;

### VNC

VNC (5900/tcp)

Version detection + NSE scripts

    nmap -Pn -sV -p 5900 --script="banner,(vnc* or realvnc* or ssl*) and not (brute or broadcast or dos or external or fuzzer)" --script-args="unsafe=1" -oN "tcp_5900_vnc_nmap.txt" <hostname>

### AJP

AJP (8009/tcp)

Version detection + NSE scripts

    nmap -Pn -sV -p 8009 -n --script ajp-auth,ajp-headers,ajp-methods,ajp-request -oN tcp_8009_ajp_nmap.txt <hostname>

## Active Directory

Enumerate users

    net user
    net user /domain
    net user $domain_user /domain

Enumerate groups

    net group /domain

Includes domain users that are part of local administrators group

    net localgroup administrators

### PowerView

Import PowerView

    PS> Import-Module .\PowerView.ps1

Get info about current domain

    PS> Get-NetDomain

List members of Domain Admins group

    PS> Get-NetGroupMember -GroupName "Domain Admins"

List all computers in domain

    PS> Get-NetComputer

Enumerate logged-on users
NB: only lists users logged on to target if we have local administrator privileges on target

    PS> Get-NetLoggedon -ComputerName $hostname

Enumerate active user sessions on servers e.g. file servers or domain controllers

    PS> Get-NetSession -ComputerName $hostname

Enumerate SPNs

    PS> Get-NetUser -SPN | select serviceprincipalname