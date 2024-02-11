Enumeration Resumo
========================

## Enumeration to exploitation

basic nmap TCP

    sudo nmap_enum <hostname> | tee nmap_output.txt

basic nmap UDP
    
    sudo nmap -sU -p 53,161,111,137,139,500,2049 -Pn <hostname> | tee nmap_udp_output.txt

All ports nmap

    sudo nmap -p- -Pn -T5 -n <hostname> | tee nmap_fullportstcp_output.txt

Vulns scan nmap

    sudo nmap -sS --script vuln -n -T5 -Pn <hostname> -p <ports> | tee nmap_vulns.txt

### HTTP 

URL brute force common without extensions

    gobuster dir -u http://<hostname> -w /usr/share/seclists/Discovery/Web-Content/common.txt -k -t 16 -o "tcp_port_protocol_s_ext_gobuster.txt"

URL brute force common w/ extensions

    gobuster dir -u http://<hostname> -w /usr/share/seclists/Discovery/Web-Content/common.txt -x "txt,html,php,asp,aspx,jsp" -k -t 16 -o "tcp_port_protocol_ext_gobuster.txt"
    
URL brute force medium w/ extensions

    gobuster dir -u http://<hostname> -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -x "txt,html,php,asp,aspx,jsp" -k -t 16 -o "tcp_port_protocol_gobuster.txt"

URL brute force medium w/ extensions fuff

    ffuf -u http://<hostname>/FUZZ -ic -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -e ".txt,.html,.php,.asp,.aspx,.jsp" -x http://127.0.0.1:8080 -fs <filtros>

Web scan nikto

    nikto -host http://<hostname> -T x 6 | tee nikto_output.txt

Subdomain enumeration

    ffuf -c -ic -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -H "Host: FUZZ.<hostname>" -u http://<hostname> -fs xxx



### SMB e RPC

SMB Null session 1

    smbclient -L //<hostname>

SMB Null session 2
    
    smbclient -L //<hostname> -U '' -N

SMB Null session 3
    
    smbclient -L //<hostname> -U ''

RPC Null session
    
    rpcclient -U '' -N <hostname>
    srvinfo
    enumdomusers
    getdompwinfo
    querydominfo
    netshareenum
    netshareenumall

Enum4linux
    
    enum4linux -a <hostname>

### DNS
Dig
    
    dig axfr domain.com @dns_server

DNSEnum
    
    dnsenum -dnsserver 10.10.10.123 friedzone.red

dnsrecon
    
    dnsrecon -d friendzone.red -n 10.10.10.123
    
### SMTP

    nmap -sS -p 25 -n -sV 10.11.1.25
    
    nc -nv 10.11.1.217 25
    VRFY root
    VRFY idontexist
    
    VRFY <username>
    
    user <username>
    pass <password>
    list
    
    retr 1
    retr 2
    

### SNMP

Bruteforce of community names

    onesixtyone <hostname> -c communities.txt

write access check

    snmp-check -w -c public <hostname>

nmap snmp enumeration
    
    sudo nmap -sU -p 161 --script "snmp*" <hostname>

snmpwalk
    
    snmpwalk -c public -v 2c <hostname>

**Importante notar que este comando consegue obter mais informações a respeito do serviço SNMP, do servidor, inclusive enumerar usuários e arquivos do sistema***

    snmpwalk -c public -v2c <hostname> . | tee /tmp/snmpwalk_full.txt

    snmpwalk -c public -v1 <hostname> . | tee /tmp/snmpwalk_full.txt

Vale considerar que temos que observar as pastas que conseguirmos ver de webservers, por exemplo, além de credenciais em texto claro em linha de comando, também. Foram os cenários que observei no hack the box

snmpbulkwalk e snmp_process_list.py

    sudo apt update
    sudo apt install snmp-mibs-downloader

    snmpbulkwalk -c public -v2c <hostname> | tee snmpbulk.txt

    python snmp_process_list.py snmpbulk.txt

## Vale considerar alguns pontos aqui, tais como:

1) Enumeração de domínios via certificado com sslscan, por exemplo:

    sslscan <hostname>

2) Verificar as credenciais padrão das páginas web (sistemas já conhecidos)
3) Verificar as credenciais (as default e as que conseguimos via enumeração) obtidas e realizar o bruteforce em TODOS os outros SERVIÇOS encontrados na máquina


![scribble](../../media/Enumeration-Exploitation.drawio.png)
