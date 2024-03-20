Web Enumeration
=======================
Checklist

- Start by **identifying** the **technologies** used by the web server. Look for **tricks** to keep in mind during the rest of the test if you can successfully identify the tech.Any **known vulnerability** of the version of the technology?
- Using any **well known tech**? Any **useful trick** to extract more information?
- Any **specialised scanner** to run (like wpscan)?
- Launch **general purposes scanners**. You never know if they are going to find something or if the are going to find some interesting information.
- Start with the **initial checks**: **robots**, **sitemap**, **404** error and **SSL/TLS scan** (if HTTPS).
- Start **spidering** the web page: It's time to **find** all the possible **files, folders** and **parameters being used.** Also, check for **special findings**.*Note that anytime a new directory is discovered during brute-forcing or spidering, it should be spidered.*
- **Directory Brute-Forcing**: Try to brute force all the discovered folders searching for new **files** and **directories**.*Note that anytime a new directory is discovered during brute-forcing or spidering, it should be Brute-Forced.*
- **Backups checking**: Test if you can find **backups** of **discovered files** appending common backup extensions.
- **Brute-Force parameters**: Try to **find hidden parameters**.
- Once you have **identified** all the possible **endpoints** accepting **user input**, check for all kind of **vulnerabilities** related to it.


## 1 - Directory enumeration

Mais básico e lento

    dirb http://<hostname>

Somente diretórios básico (sem considerar extensões dos arquivos)

    gobuster dir --useragent "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0" -u http://<hostname> -w /usr/share/seclists/Discovery/Web-Content/common.txt -k -t 16 -o "tcp_port_protocol_s_ext_gobuster.txt"

Com extensões básico (common.txt)

    gobuster dir --useragent "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0" -u http://<hostname> -w /usr/share/seclists/Discovery/Web-Content/common.txt -x "txt,html,php,asp,aspx,jsp" -k -t 16 -o "tcp_port_protocol_gobuster.txt"

Somente diretórios (SEM EXTENSÕES)

    gobuster dir --useragent "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0" -u http://<hostname> -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -k -t 16 -o "tcp_port_protocol_gobuster.txt"
    
COM EXTENSÕES (MAIS DEMORADO)
    
    gobuster dir --useragent "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0" -u http://apt.htb -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -x "txt,html,php,asp,aspx,jsp" -k -t 16 -o "tcp_port_protocol_gobuster.txt"

<https://www.invicti.com/s/research/SVNDigger.zip>

**CONSIDERAR AS SEGUINTES EXTENSÕES TAMBÉM**


    .zip , .tar , .gz , .tgz , .rar , etc.: (Compressed) archive files
    .java : No reason to provide access to Java source files
    .txt : Text files
    .pdf : PDF documents
    .docx , .rtf , .xlsx , .pptx , etc.: Office documents
    .bak , .old and other extensions indicative of backup files (for example: ~ for Emacs backup files)


## 2 - Front end code review comments and recon

### clone a website

    wget --header "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0" -mk -nH http://domain.com

    wget --header "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0" -r -l 8 --no-check-certificate https://domain.com 

**Vale considerar que estes comandos não obtém todo o site caso ele seja construido com JS**

Com proxy e rodando autenticado.

    wget -r -l 8 --header='Cookie: OAMAuthnHintCookie=<<XXX>>; LoginCorpCookie=<<XXX>>' --header "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0" --header='Authorization: Bearer <<JWT_HERE>>' --no-check-certificate -e https_proxy=127.0.0.1:8080 https://domain.com.br/url/

--spider: essa opção faz com que o wget não baixe as páginas
-r recursivo
-l level que pode chegar a partir do root directory, seria o primeiro level
--no-check-certificte ignorar erros de certificado

**Esse não é sempre que funciona, especialmente para sites com HTTPS**

    httrack https://<hostname>
    cat hts-cache/new.txt | cut -f 8

Depois disso, podemos extrair as URLs dos JSs que foram baixados junto as páginas com o [extract.rb](https://github.com/jobertabma/relative-url-extractor.git)

**Eu baixei esse cara no meu kali e esttou rodando ele com o comando extract.rb**

    cat main.e82eb4c5.js | extract.rb
    cat *.js | extract.rb
    cat index.html | extract.rb
    cat *.html | extract.rb

### wappalyzer and whatweb

https://addons.mozilla.org/pt-BR/firefox/addon/wappalyzer/

    whatweb -a 1 https://domain.com.br
    whatweb -a 3 https://domain.com.br
    whatweb -a 4 https://domain.com.br

### httprint

    sudo apt-get update
    sudo apt-get install httprint
    httprint -h domain.com.br -s /usr/share/httprint/signatures.txt
    httprint -P0 -h https://domain.br -s /usr/share/httprint/signatures.txt

-P0 é para evitar pings

### netcat

    nc domain.com.br 80
    HEAD / HTTP/1.1
    Host: domain.com.br
    
    

### nmap

    sudo nmap -p 80,443 -sV -sS domain.com.br
    sudo nmap -p 80,443 -sS domain.com.br --script http-enum
    
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

### whatweb

    whatweb -a 4 https://domain.com.br

### nikto

    nikto -host http://<hostname> -T x 6 -useragent "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0" | tee nikto_output.txt

### nmap

    nmap -sS --script http* 10.11.1.123

### waf detection

    nmap -Pn --script http-waf-fingerprint domain.com.br --script-args http-waf-fingerprint.intensive=1 -p443,80
    
    nmap -Pn --script http-waf-detect domain.com.br --script-args="http-waf-detect.aggro " -p443,80
    
    wafw00f https://<hostname>/

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


## 5 - Methods enumeration


    nmap -p 443 --script http-methods --script-args http-methods.url-path='/index.php' domain.com.br

Testing PUT method

```
PUT /webcrm/test HTTP/2
Host: maykleads.makesystem.com.br
Content-Length: 43

<html>
HTTP PUT Method is Enabled
</html>

# ou

PUT /webcrm/test HTTP/2
Host: maykleads.makesystem.com.br
Content-Length: 43
Content-Type: application/html
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0

<html>
HTTP PUT Method is Enabled
</html>
```
ou podemos utilizar o curl também

    curl -X PUT http://127.0.0.1:9001/root/.ssh/authorized_keys -d 'content'
    
    curl -X PUT https://domain.com.br/url/test.html -H 'User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0' -d '<html>HTTP PUT Method is Enabled</html>'
    
    ##### Testing with X-HTTP-Method header
    curl -X PUT https://maykleads.makesystem.com.br/webcrm/test.html -H 'User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0' -H 'X-HTTP-Method: PUT' -d '<html>HTTP PUT Method is Enabled</html>'


## 6 - Enumeracoes especificas

Look for links to other files inside the CSS files.

If you find a .git file some information can be extracted

If you find a .env information such as api keys, dbs passwords and other information can be found.

If you find API endpoints you should also test them. These aren't files, but will probably "look like" them.

JS files: In the spidering section several tools that can extract path from JS files were mentioned. Also, It would be interesting to monitor each JS file found, as in some ocations, a change may indicate that a potential vulnerability was introduced in the code.

Drupal

    python3 drupwn --version 7.28 --mode enum --target $url

    droopescan scan drupal -u $url

wordpress

Wordpress

Enumerate vulnerable plugins and themes, timthumbs, wp-config.php backups, database exports, usernames and media IDs

    wpscan --url http://<hostname>/webservices/wp --plugins-detection aggressive

    wpscan --url http://10.10.10.37 -e ap,t,tt,u --plugins-detection aggressive --plugins-version-detection aggressive

    wpscan --url http://<hostname> --no-update --disable-tls-checks -e vp,vt,tt,cb,dbe,u,m --plugins-detection aggressive --plugins-version-detection aggressive -f cli-no-color 2>&1 | tee tcp_port_protocol_wpscan.txt

Enumerate all plugins

    wpscan --url $url --disable-tls-checks --no-update -e ap --plugins-detection aggressive -f cli-no-color 2>&1 | tee tcp_port_protocol_wpscan_plugins.txt

## 7 - Enumeração de parâmetros

    ffuf -c -ic -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt -u http://apt.htb/clients.html?FUZZ= -fs 12146
    
    ffuf -c -ic -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt -u http://<hostname>/url?FUZZ= -fs xxxx

Enumeração autenticado:

    ffuf -c -ic -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt -b 'Cookie_name1=xxxx; Cookie_name2=xxxx' -H 'Authorization: Bearer xxx' -u https://domain.br/esim-activation-bff/v1/qrcode?FUZZ -p 10 -fs 111,0 -x http://127.0.0.1:8080
    
## 8 - Others

### 502 Proxy Error

If any page responds with that code, it's probably a bad configured proxy. If you send a HTTP request like: GET https://google.com HTTP/1.1 (with the host header and other common headers), the proxy will try to access google.com and you will have found a SSRF.

### NTLM Authentication - Info disclosure

If the running server asking for authentication is Windows or you find a login asking for your credentials (and asking for domain name), you can provoke an information disclosure.
Send the header: “Authorization: NTLM TlRMTVNTUAABAAAAB4IIAAAAAAAAAAAAAAAAAAAAAAA=” and due to how the NTLM authentication works, the server will respond with internal info (IIS version, Windows version...) inside the header "WWW-Authenticate".
You can automate this using the nmap plugin "http-ntlm-info.nse".

