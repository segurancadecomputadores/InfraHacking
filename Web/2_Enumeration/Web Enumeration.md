Web Enumeration
=======================

# Active information gathering


## 1 - Website recon
  Navigate manually through the application

#### clone a website

    wget -mk -nH http://domain.com

#### wappalyzer

https://addons.mozilla.org/pt-BR/firefox/addon/wappalyzer/

#### httprint

    sudo apt-get update
    sudo apt-get install httprint
    httprint -h assine.vivo.com.br -s /usr/share/httprint/signatures.txt

#### netcat

    nc assine.vivo.com.br 80
    HEAD / HTTP/1.1
    Host: assine.vivo.com.br
    
    

#### nmap

    sudo nmap -p 80,443 -sV -sS assine.vivo.com.br
    sudo nmap -p 80,443 -sV -sS assine.vivo.com.br --script httprecon-nse/httprecon.nse (não tive sucesso com esse script)
    sudo nmap -p 80,443 -sS assine.vivo.com.br --script http-enum
    
### 2 - searching for subdomains

#### dnsrecon
    dnsrecon -d target.com -D wordlist.txt -t brt
    
#### dig

#### nmap
source:
https://medium.com/@int0x33/day-63-top-10-essential-nmap-scripts-for-web-app-hacking-c7829ff5ab7

Attempts to enumerate DNS hostnames by brute force guessing of common subdomains. With the dns-brute.srv argument, dns-brute will also try to enumerate common DNS SRV records.


    nmap -sS -A -T4 -Pn -sV --version-intensity 9 <alvo> -p...
    
    -sS => Syn scan
    -A  => Run basic vuln scanner
    -T4 => Mais efetivo que o T5
    -Pn => Não pingar o alvo
    -sV => get service version
    --version-intensity 9 => Forma mais agressiva de obter a versão dos serviços
    

    nmap --script dns-brute --script-args dns-brute.domain=foo.com,dns-brute.threads=6,dns-brute.hostlist=./hostfile.txt,newtargets -sS -p 80

    nmap --script=http-backup-finder mercadobitcoin.com.br -Pn
    Starting Nmap 7.80 ( https://nmap.org ) at 2020-03-31 10:55 EDT
    Nmap scan report for mercadobitcoin.com.br (104.17.118.69)
    Host is up (0.019s latency).
    Other addresses for mercadobitcoin.com.br (not scanned): 104.17.117.69 2606:4700::6811:7545 2606:4700::6811:7645
    Not shown: 998 filtered ports
    PORT     STATE SERVICE
    443/tcp  open  https
    8443/tcp open  https-alt


#### sublist3r

python sublist3r -d \<domain\>

![qownnotes-media-eYJOpJ](../../../media/29314.png)


## 3 - Recoinnassence

Desenvolvi uma ferramenta que automatiza uma parte desse processo. Ela utiliza das ferramentas httrack, nikto, httprint, nmap e netcat para uma verificação básica de potas 443 ou 80

    #!/bin/bash

    file_name=$1

    for domain_name in `cat $file_name`
    do
        theHarvester -d  $domain_name -b all -l 500 > ${domain_name}_theHarverster.txt

        nmap -sS -sV -Pn --script vuln $domain_name --webxml -oX "${domain_name}_nmap.xml"

        nc -v -z -w 3 $domain_name 443

        if [ $? == 0 ]
        then
                httrack https://$domain_name

                httprint -h https://$domain_name -s /usr/share/httprint/signatures.txt -P0 -ua "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/101.0.4951.61 Safari/537.36"

                nikto -Display V -h https://$domain_name -nossl -Tuning x 6 -o "${domain_name}_nikto.html" -Format htm
        else
                httrack $domain_name

                httprint -h http://$domain_name -s /usr/share/httprint/signatures.txt -P0 -ua "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/101.0.4951.61 Safari/537.36"

                nikto -Display V -h http://$domain_name -nossl -Tuning x 6 -o "${domain_name}_nikto.html" -Format htm
        fi

    done

## Scanning


	
### nmap

    nmap -sS --script http* 10.11.1.123


## Searching for leaks

### HaveIBeenPwned





## the Harvester
=========

theHarvester -d example.domain.com -l 500 -b all

### waf detection

    nmap -Pn --script http-waf-fingerprint meuvivo.vivo.com.br --script-args http-waf-fingerprint.intensive=1 -p443,80
    
    nmap -Pn --script http-waf-detect meuvivo.vivo.com.br --script-args="http-waf-detect.aggro " -p443,80


## Recon

bgp.he.net

por meio deste cara, obter os números de ASN
Obter os ranges de IP
ASN - > Cada continente terá um ARIN (exemplo) pra definir os ranges de IP de cada continente
asnroutes
aggro

# Identificar os domain controllers de uma rede

Use Nslookup
Nslookup is a command-line tool that displays information you can use to diagnose Domain Name System (DNS) infrastructure.

To use Nslookup to verify the SRV records, follow these steps:

On your DNS, select Start > Run.
In the Open box, type cmd.
Type nslookup, and then press ENTER.
Type set type=all, and then press ENTER.
Type _ldap._tcp.dc._msdcs.Domain_Name, where <Domain_Name> is the name of your domain, and then press ENTER.

# Conclusão




alison.costa@telefonica.com
9HNKyu3Pn5EZfjY
