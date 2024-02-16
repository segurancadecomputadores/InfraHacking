Web Enumeration
=======================

## 1 - Directory enumeration

Mais básico e lento

    dirb http://<hostname>

Somente diretórios básico (sem considerar extensões dos arquivos)

    gobuster dir -u http://<hostname> -w /usr/share/seclists/Discovery/Web-Content/common.txt -k -t 16 -o "tcp_port_protocol_s_ext_gobuster.txt"

Com extensões básico (common.txt)

    gobuster dir -u http://<hostname> -w /usr/share/seclists/Discovery/Web-Content/common.txt -x "txt,html,php,asp,aspx,jsp" -k -t 16 -o "tcp_port_protocol_gobuster.txt"

Somente diretórios (SEM EXTENSÕES)

    gobuster dir -u http://<hostname> -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -k -t 16 -o "tcp_port_protocol_gobuster.txt"
    
COM EXTENSÕES (MAIS DEMORADO)
    
    gobuster dir -u http://apt.htb -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -x "txt,html,php,asp,aspx,jsp" -k -t 16 -o "tcp_port_protocol_gobuster.txt"



## 2 - Recoinnassence

### clone a website

    wget -mk -nH http://domain.com

    wget -r -l 8 --no-check-certificate https://domain.com 

--spider: essa opção faz com que o wget não baixe as páginas
-r recursivo
-l level que pode chegar a partir do root directory, seria o primeiro level
--no-check-certificte ignorar erros de certificado

    httrack https://atsserver.acute.local
    cat new.txt | cut -f 8

### wappalyzer

https://addons.mozilla.org/pt-BR/firefox/addon/wappalyzer/

### httprint

    sudo apt-get update
    sudo apt-get install httprint
    httprint -h assine.vivo.com.br -s /usr/share/httprint/signatures.txt

### netcat

    nc assine.vivo.com.br 80
    HEAD / HTTP/1.1
    Host: assine.vivo.com.br
    
    

### nmap

    sudo nmap -p 80,443 -sV -sS assine.vivo.com.br
    sudo nmap -p 80,443 -sV -sS assine.vivo.com.br --script httprecon-nse/httprecon.nse (não tive sucesso com esse script)
    sudo nmap -p 80,443 -sS assine.vivo.com.br --script http-enum
    
Desenvolvi uma ferramenta que automatiza uma parte desse processo. Ela utiliza das ferramentas httrack, nikto, httprint, nmap e netcat para uma verificação básica de potas 443 ou 80

```
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
```

## 3 - Scanning

### nikto

    nikto -host http://<hostname> -T x 6 | tee nikto_output.txt

### nmap

    nmap -sS --script http* 10.11.1.123

### waf detection

    nmap -Pn --script http-waf-fingerprint meuvivo.vivo.com.br --script-args http-waf-fingerprint.intensive=1 -p443,80
    
    nmap -Pn --script http-waf-detect meuvivo.vivo.com.br --script-args="http-waf-detect.aggro " -p443,80

## 4 - searching for subdomains

### fuff

    ffuf -c -ic -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -H "Host: FUZZ.<hostname>" -u http://<hostname> -fs xxxx

### dnsrecon
    dnsrecon -d target.com -D wordlist.txt -t brt
    

### nmap
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

### sublist3r

python sublist3r -d \<domain\>

![qownnotes-media-eYJOpJ](../../../media/29314.png)


## 5 - Enumeracoes especificas

Drupal

    python3 drupwn --version 7.28 --mode enum --target $url

    droopescan scan drupal -u $url

wordpress

Wordpress

Enumerate vulnerable plugins and themes, timthumbs, wp-config.php backups, database exports, usernames and media IDs

    wpscan --url http://10.10.10.37 -e ap,t,tt,u --plugins-detection aggressive --plugins-version-detection aggressive

    wpscan --url http://<hostname> --no-update --disable-tls-checks -e vp,vt,tt,cb,dbe,u,m --plugins-detection aggressive --plugins-version-detection aggressive -f cli-no-color 2>&1 | tee tcp_port_protocol_wpscan.txt

Enumerate all plugins

    wpscan --url $url --disable-tls-checks --no-update -e ap --plugins-detection aggressive -f cli-no-color 2>&1 | tee tcp_port_protocol_wpscan_plugins.txt

