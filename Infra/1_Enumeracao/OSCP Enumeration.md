OSCP Enumeration
========================================

<https://github.com/oncybersec/oscp-enumeration-cheat-sheet/blob/main/README.md>


OSCP Enumeration Cheat Sheet
A collection of commands and tools used for conducting enumeration during my OSCP journey.

Description
This is an enumeration cheat sheet that I created while pursuing the OSCP. It also includes the commands that I used on platforms such as Vulnhub and Hack the Box. Some of these commands are based on those executed by the Autorecon tool.

Disclaimer
This cheat sheet should not be considered to be complete and only represents a snapshot in time when I used these commands for performing enumeration during my OSCP journey. These commands should only be used for educational purposes or authorised testing.

Table of Contents

- [Host Discovery](#host-discovery) 
- [Port scan](#portscan)
- [Proxychains](#proxychains)
- [Autorecon](#autorecon)
- [Services](#services)
    - [FTP (21/tcp)](#ftp)
    - [SSH (22/tcp)](#ssh)
    - [SMTP (25/tcp)](#smtp)
    - [DNS (53/tcp, 53/udp)](#dns)
    - [HTTP/HTTPS (80/tcp, 443/tcp)](#http)
    - [Kerberos (88/tcp, 464/tcp)](#kerberos)
    - [POP3/POP3S (110/tcp, 995/tcp)](#pop)
    - [RPC (111/tcp, 135/tcp)](#rpc)
    - [ident (113/tcp)](#ident)
    - [NTP (123/udp)](#ntp)
    - [NetBIOS-NS (137/udp)](#netbios%20ns)
    - [SMB (139/tcp, 445/tcp)](#smb)
    - [IMAP/IMAPS (143/tcp, 993/tcp)](#imap)
    - [SNMP (161/udp)](#snmp)
    - [LDAP (389/tcp, 3268/tcp, 636/tcp)](#ldap)
    - [Java RMI (1100/tcp)](#java%20rmi)
    - [MSSQL (1433/tcp)](#mssql)
    - [Oracle TNS listener (1521/tcp)](#oracletnslistener)
    - [NFS (2049/tcp)](#nfs)
    - [MySQL (3306/tcp)](#mysql)
    - [RDP (3389/tcp)](#rdp)
    - [SIP (5060/udp)](#sip)
    - [PostgreSQL (5432/tcp)](#postgresql)
    - [VNC (5900/tcp)](#vnc)
    - [AJP (8009/tcp)](#ajp)


## PortScan

### nmap

Full TCP port scan
    
    sudo nmap -Pn -p- -oN alltcp_ports.txt <hostname>

Full TCP port scan (safe scripts + version detection)
    
    sudo nmap -Pn -sC -sV -p- -oN alltcp.txt <hostname>

Top 20 UDP port scan

    sudo nmap -Pn -sU -sV -sC --top-ports=20 -oN top_20_udp_nmap.txt <hostname>

    nmap -sU -p 53,161,111,137,139,500,2049 -Pn 10.11.1.0/24

### netcat

    nc -z -w 1 -n -v 10.11.1.70 500
    nc -z -w 1 -n -v 10.11.1.70 500-1000

    nc -u -z -w 1 -n -v 10.11.1.70 161

### bash pseudo devices

    /dev/$protocol/$host/$port

    if timeout 5 bash -c '</dev/tcp/kernel.org/443 &>/dev/null'
    then
      echo "Port is open"
    else
      echo "Port is closed"
    fi

ou

    timeout .1 bash -c "echo /dev/tcp/10.11.1.5/80" && echo "port 80 is open"

### Legion (gui app)

    sudo legion


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

    nmap -sS -p 25 -n -sV 10.11.1.25
    
    nmap -Pn -sV -p 25 "--script=banner,(smtp* or ssl*) and not (brute or broadcast or dos or external or fuzzer)" -oN tcp_25_smtp_nmap.txt <hostname>

smtp-user-enum

    /home/kali/.local/bin/smtp-user-enum -V -m RCPT -w -f '<user@example.com>' -d 'domain.local' -U "/usr/share/metasploit-framework/data/wordlists/unix_users.txt" <hostname> 25 2>&1 | tee "tcp_25_smtp_user-enum.txt"

    nc -nv 10.11.1.217 25
    #ou
    telnet 10.10.10.10 25
    
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
Vale considerar a entrada de DNS no arquivo resolv.conf:

    echo "nameserver <ip_dns_server>" >> /etc/resolv.conf

    dig axfr @<hostname> $domain 2>&1 | tee "tcp_53_dns_dig.txt"

    host -t axfr <hostname> $dns_server
    
    dnsenum htb.local
    
    dnsrecon -d _msdcs.thinc.local -D /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -t brt

Perform reverse DNS lookup (may display NS record containing domain name)

    nslookup <hostname> <hostname>

Brute force subdomains
   
    gobuster dns -d $domain -w /usr/share/seclists/Discovery/DNS/bitquark-subdomains-top100000.txt -t 16 -o "tcp_53_dns_gobuster.txt"
    
#### Enumerate domain

Primeiro temos que verificar se a gente consegue obter o nome do domínio por mieo de uma resolução invesrsa de DNS da seguinte maneira:
Informar o serviço de DNS encontrado por meio do comando abaixo:
    
    sudo nmap -sU -p 53 192.168.134.0/24 -oG dns_enum_lab.txt
    #Assim que achado o DNS server (porta 53 aberta), fazemos um nslookup ou host para um host da rede
    #host <IPS_DA_REDE> <IP_DNS_SERVER>
    host 192.168.134.149 192.168.134.149
    

    nslookup
    server 192.168.134.149    
![qownnotes-media-GwrWbS](../../media/qownnotes-media-GwrWbS.png)

    host -t ns mailman.com # Para encontrar o name server do domínio


#### Enumerating subdomains


Brute-force: 

	dnsrecon -d target.com -D wordlist.txt -t brt
	
**OBS: wordlist can be found at:**

**C:\Users\olive\Desktop\acosta\owncloud\Tools_Utilities\hacking-tools\fuzzdb\discovery\dns**


#### DNS cache snooping: 
	
	dnsrecon -t snoop -D wordlist.txt -n 2.2.2.2 where 2.2.2.2 is the IP of the target’s NS server
Options
--threads 8: Number of threads
-n nsserver.com: Use a custom name server
Output options
--db: SQLite 3 file
--xml: XML file
--json: JSON file
--csv: CSV file

#### DNS Zone transfer

	dnsrecon -d target.com -t axfr
	
or
	
	host -l target.com <dns_server_address>
	
**OBS: First find which dns servers correspond to that domain with:**
	
	host -t ns target.com
	
![qownnotes-media-XJyjiZ](file://media/18886.png)

DNS Query types:

https://ns1.com/resources/dns-types-records-servers-and-queries

#### Enumeração de DNS (domínio)

Preimeiro temos que identificar qual o servidor de DNS que responde por este serviço:

    nmap -p 53 -sU --open 10.11.1.0/24 -oG dns_servers.txt
    
Assim que obtido o servidor, ou seja, obter a resposta do comando acima para os IPs que responderam com "open", configuramos ele no arquivo /etc/resolv.conf ccom a seguinte diretiva:

    nameserver <IP_SERVER_DNS>

Feito isso, executamos o seguinte comando para obter o nome de domínio da rede alvo:

    fping -d -a -g 10.0.0.0/24 # -> reverse DNS lookup   
    #ou
    fping -d -A -a -g 10.0.0.0/24 #(para obter o endereço de IP ao lado do hostname)
    
![qownnotes-media-RkNxKi](../../../media/qownnotes-media-RkNxKi.png)


Depois, tentamos realizar uma transferência de zona para obter informações a respeito do domínio:

    host -t axfr thinc.local <IP_DNS_SERVER>
    #ou
    dig axfr thinc.local @<IP_DNS_SERVER> #Esse não funionou e não entendi o porquê
    #ou
    dnsenum thinc.local # pode ser informado a flag --dnsserver <IP_DNS_SERVER> para especificar o server de DNS
    
![qownnotes-media-TnBCdx](../../../media/qownnotes-media-TnBCdx.png)


Dessa forma obtemos todas as informações a respeito daquele domínio. ALém disto, observamos um subdomínio especial ali e tentamos uma transferência de zona lá também, sem sucesso, mas anda temos mais uma possibilidade que é enumerar os subdomínios da seguinte forma:

    dnsrecon -d _msdcs.thinc.local -D /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -t brt 

### HTTP/HTTPS

HTTP/HTTPS (80/tcp, 443/tcp)

Procurar por comentários nas páginas para verificar alguma informação sensível ou credencial

Criar wordlists com cewl se houver usuários ou possíveis senhas nas páginas:

#### Cewl

    cewl $url/index.php -m 3 --with-numbers -w cewl.txt
    

Version detection + NSE scripts

    nmap -Pn -sV -p $port "--script=banner,(http* or ssl*) and not (brute or broadcast or dos or external or http-slowloris* or fuzzer)" -oN tcp_port_protocol_nmap.txt <hostname>

    sudo nmap -sS -p 80,443 --script "http* and not http-brute and not http-slowloris" -n 10.11.1.123

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
 
```
kerbrute userenum -d domain --dc <hostname> users.txt
```

### POP3/POP3S

POP3/POP3S (110/tcp, 995/tcp)

Version detection + NSE scripts

    nmap -Pn -sV -p $port "--script=banner,(pop3* or ssl*) and not (brute or broadcast or dos or external or fuzzer)" -oN tcp_port_pop3_nmap.txt <hostname>

Interagindo com o serviço:

    telnet <hostname> 110

    USER admin
    PASS P@ssw0rd
    
    list
    
    retr 1
    retr 2

Podemos verificar via SSL também com o seguinte comando:

    openssl s_client -starttls pop3 -connect <hostname>:110



### RPC

RPC (111/tcp, 135/tcp)
msrpc/rpcbind


Version detection + NSE scripts

    nmap -Pn -sV -p $port --script=banner,msrpc-enum,rpc-grind,rpcinfo -oN tcp_port_rpc_nmap.txt <hostname>  

impacket

    impacket-rpcmap -brute-uuids -brute-opnums ncacn_ip_tcp:<hostname>
    
 
    impacket-rpcdump <hostname>


rpcinfo

List all registered RPC programs

    rpcinfo -p <hostname>    

Provide compact results

    rpcinfo -s <hostname>

Null session

Aqui um equívocona questão do rpcclient. Este client utiliza das portas 139 e 445 para comunicação.

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

smb-os-discovery, smb-enum-processes,smb-system-info.nse

smbmap

List share permissions

    smbmap -H <hostname> -P 445 2>&1 | tee -a "smbmap-share-permissions.txt"; smbmap -u null -p "" -H <hostname> -P 445 2>&1 | tee -a "smbmap-share-permissions.txt"

List share contents

    smbmap -H <hostname> -P 445 -R 2>&1 | tee -a "smbmap-list-contents.txt"; smbmap -u null -p "" -H <hostname> -P 445 -R 2>&1 | tee -a "smbmap-list-contents.txt"

enum4linux

    enum4linux -a -M -l -d <hostname> 2>&1 | tee "enum4linux.txt"

Agressive mode

    enum4linux -a -A <IP_VICTIM>



Enumerate Samba version (*nix)

NB: change interface tcpdump listening on

    sudo ./smbver.sh <hostname> 139

Null session

    smbmap -H <hostname>
    smbclient -L //<hostname>
    cme smb 10.11.1.xxx -u '' -p ''
    cme smb 10.11.1.xxx -u 'guest' -p ''


    smbclient -L //10.10.10.103
    smbclient -L -U '' -N //10.10.10.103    

Para conseguir fazer enunmeração mais rapidamente, é interessante que utilizemos o recurso de recursividade no smbclient com os seguintes comandos:

    recurse on
    dir

Enumerate shares

    nmap --script smb-enum-shares -p 445 <hostname>

Connect to wwwroot share (try blank password)

    smbclient \\\\<hostname>\\wwwroot

Nmap scans for SMB vulnerabilities (NB: can cause DoS)

RRAS Service Overflow
<https://docs.microsoft.com/en-us/security-updates/securitybulletins/2006/ms06-025>

RCE vulnerabilities

    nmap -Pn -sV -p 445 --script="smb-vuln-ms06-025" --script-args="unsafe=1" -oN "tcp_445_smb_ms06-025.txt" <hostname>

DNS RPC Service Overflow
,https://docs.microsoft.com/en-us/security-updates/securitybulletins/2007/ms07-029>

    nmap -Pn -sV -p 445 --script="smb-vuln-ms07-029" --script-args="unsafe=1" -oN "tcp_445_smb_ms07-029.txt" <hostname>

Server Service Vulnerability
<https://docs.microsoft.com/en-us/security-updates/securitybulletins/2008/ms08-067>

    nmap -Pn -sV -p 445 --script="smb-vuln-ms08-067" --script-args="unsafe=1" -oN "tcp_445_smb_ms08-067.txt" <hostname>

**Esse comando acima pode derrubar o servidor, então temos que ficar atentos com isso em redes produtivas.**


Eternalblue
<https://docs.microsoft.com/en-us/security-updates/securitybulletins/2017/ms17-010>

    nmap -p 445 --script smb-vuln-ms17-010 -oN "tcp_445_smb_ms08-067.txt" <hostname>

Podemos considerar algumas ferramentas para fazer a checagem desse serviço. São elas:

### IMAP/IMAPS

IMAP/IMAPS (143/tcp, 993/tcp)

Version detection + NSE scripts

    nmap -Pn -sV -p $port "--script=banner,(imap* or ssl*) and not (brute or broadcast or dos or external or fuzzer)" -oN tcp_port_imap_nmap.txt <hostname>  

### SNMP

SNMP (161/udp)

Version detection + NSE scripts

    sudo nmap -Pn -sU -sV -p 161 --script="banner,(snmp* or ssl*) and not (brute or broadcast or dos or external or fuzzer)" -oN "udp_161_snmp-nmap.txt" <hostname>       

snmp enumeration

```
    sudo nmap -sU --open -p 161 10.11.1.1-254 -oG open-snmp.txt

There is a module on metasploit:
auxiliary/scanner/snmp/snmp_enum
set RHOSTS `ip`

default configuration
Module options (auxiliary/scanner/snmp/snmp_enum):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   COMMUNITY  public           yes       SNMP Community String
   RETRIES    1                yes       SNMP Retries
   RHOSTS     10.12.24.1       yes       The target address range or CIDR identifier
   RPORT      161              yes       The target port (UDP)
   THREADS    1                yes       The number of concurrent threads
   TIMEOUT    1                yes       SNMP Timeout
   VERSION    1                yes       SNMP Version <1/2c>

```
![qownnotes-media-gSQJLi](../../media/qownnotes-media-gSQJLi.png)

Considerar

#### metasploit

no metasploit podemos utilizar o módulo 

    auxiliary/scanner/snmp/snmp_enum

, desta forma, podemos informar uma comunidade que já conhecemos ou tentar enumerar comunidades com o seguinte módulo:

    auxiliary/scanner/snmp/snmp_login



#### SNMPWALK

```
snmpwalk -c public -v1 -t 10 10.11.1.14
```

**Windows users**

```
snmpwalk -c public -v1 10.11.1.14 1.3.6.1.4.1.77.1.2.25
```

**Windows processes**

```
snmpwalk -c public -v1 10.11.1.73 1.3.6.1.2.1.25.4.2.1.2
```

**Windows TCP open ports**

```
snmpwalk -c public -v1 10.11.1.14 1.3.6.1.2.1.6.13.1.3    
```

**Windows installed software:**

```
snmpwalk -c public -v1 10.11.1.50 1.3.6.1.2.1.25.6.3.1.2
```

#### onesixtyone

```
echo public > community
echo private >> community
echo manager >> community
for ip in $(seq 1 254); do echo 10.11.1.$ip; done > ips
onesixtyone -c community -i ips


onesixtyone 192.168.4.0/24 public

onesixtyone -c dict.txt -i hosts -o my.log -w 100
```

#### SNMPCHECK

```
snmp-check -w -c public <hostname>
```

#### snmpbulkwalk

```
sudo apt update
sudo apt install snmp-mibs-downloader

snmpbulkwalk -c public -v2c <hostname> | tee snmpbulk.txt
python snmp_process_list.py snmpbulk.txt
```

#### snmp_process_list.py

```
#!/usr/bin/env python3

import re
import sys
from collections import defaultdict
from dataclasses import dataclass


@dataclass
class Process:
    """Process read from SNMP"""
    pid: int
    proc: str
    args: str = ""

    def __str__(self) -> str:
        return f'{self.pid:04d} {self.proc} {self.args}'


with open(sys.argv[1]) as f:
    data = f.read()

processes = {}

for match in re.findall(r'HOST-RESOURCES-MIB::hrSWRunName\.(\d+) = STRING: "(.+)"', data):
    processes[match[0]] = Process(int(match[0]), match[1])

for match in re.findall(r'HOST-RESOURCES-MIB::hrSWRunParameters\.(\d+) = STRING: "(.+)"', data):
    processes[match[0]].args = match[1]

for p in processes.values():
    print(p)
```

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
    
![qownnotes-media-qSTWZx](../../media/qownnotes-media-qSTWZx.png)

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
    

     sudo nmap -sS -p 111,2049 --script nfs-showmount,nfs-ls 10.11.1.72
     
     
#### nfs-ls (libnfs-utils)
     
     #outra possibilidade também pode ser:
     sudo apt update
     sudo apt install libnfs-utils
     #Com isso a gente consegue fazer a enumeração das permissividades dos arquivos em modo recursivo
     nfs-ls -R nfs://10.11.1.72/home
     
     showmount -e 10.11.1.72
     
     nmap -sV -p 111 --script=rpcinfo 10.11.1.1-254

 A premissa do NFS é o mesmo do SMB, compartilhar arquivos por meio da rede, acontece que no caso do NFS não é utilizado autenticação e autorização em suas versões 3 e 2, somente um grau de seguranã maior na sua versão 4.
 
 Para explorar essa vulnerabilidade, primeiro precisamos fazer a enumeração do serviço por meio dos seguintes comandos:
 
 
     sudo nmap -sS -p 111,2049 --script nfs-showmount,nfs-ls 10.11.1.72
     #outra possibilidade também pode ser:
     sudo apt update
     sudo apt install libnfs-utils
     #Com isso a gente consegue fazer a enumeração das permissividades dos arquivos em modo recursivo
     nfs-ls -R nfs://10.11.1.72/home
     
     showmount -e 10.11.1.72
     
     nmap -sV -p 111 --script=rpcinfo 10.11.1.1-254


![qownnotes-media-wrgegz](../../media/qownnotes-media-wrgegz.png)

#### Exploitation

Assim que fizermos a enumeração, vamos identificar quais são os IDs pertinenetes aos arquivos lá constados no share via NFS, mas isso só é válido nas versões 2 e 3  do protocolo. A versão 4 já é mais segura! Basta adicionarmos o usuário e o grupo do usuário cujo uid e guid corespondem ao mesmo que foi identificao no momento da enumeração.

    sudo adduser --uid 1014 pwn
    sudo addgroup --gid 1014 pwn_group
    sudo usermod -a -G pwn_group pwn

    mount.nfs -o vers=3 10.11.1.72:/home /tmp/mount_dir
    
    

    

### MySQL

MySQL (3306/tcp)

Version detection + NSE scripts

```
nmap -Pn -sV -p 3306 --script="banner,(mysql* or ssl*) and not (brute or broadcast or dos or external or fuzzer)" -oN "tcp_3306_mysql_nmap.txt" <hostname>
```

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

        sudo nmap -sS --script vulners,rdp* 10.11.1.123

### SIP
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