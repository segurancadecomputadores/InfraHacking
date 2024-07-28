Web Enumeration
=======================
## Checklist

 - [ ] [**Navegue na aplicação**](#navegue%20pela%20aplicacao): Identifique **tecnologias da aplicação** e **entry points**
 - [ ] [**Analisar o frontend**](#analisar%20o%20frontend)
 - [ ] [**Procurar por CVEs**](#procurar%20por%20cves): Busque por vulnerabilidades já conhecidas
- [ ] [**WAF**](#enumeracao%20waf): Detectar e enumerar WAF
- [ ] [**Bruteforce de diretórios e arquivos**](#enumeracao%20de%20diretorios): Procurar **pastas/endpoints** via URL
- [ ] [**Bruteforce Arquivos de backup**](#enumeracao%20de%20diretorios): Procurar por **arquivos via URL**
- [ ] [**Revisao do código do frontend**](#revisao%20de%20codigo%20do%20frontend%20comentarios%20e%20reconhecimento): Baixe o código da aplicação
- [ ] [**Scanning**](#scanning): Rodar scan na aplicação
- [ ] [**Procurar subdomínios**](#procurar%20subdominios): **Procurar por subdomínios** via DNS ou **virtual hosts** no host alvo
- [ ] [**Brute%20Force de parâmetros**](#enumeracaoo%20de%20parametros): Encontrar **parâmetros escondidos**.
- [ ] [**Enumerações específicas**](#enumeracoes%20especificas): Executar scans específicos


## Analisar o frontend

Analisar o código do front end baixado e verificar todas as URLs existentes no site

Vide [Referências](#referencias) para obter o script urlExtract.

```
urlExtract -r 4 https://<hostname>
```

Sendo -r a profundidade (em níveis de diretórios) que a ferramenta deve alcançar.

```
httrack http://<hostname>
cd hts-cache
cat new.txt | cut -f 8
```

Se existir algum link na página HTML que não vimos, podemos usar a seguinte ferramenta para obter esse link

```
cat demo-file.js | ./extract.rb
cat demo-file.js | ./extract.rb --show-line
curl -s https://hackerone.com/hacktivity | ./extract.rb
```

[Exemplo](obsidian://open?vault=Notes&file=docs%2FInfraHacking%2FCTFs_Labs%2FBurpSuite%20Academy%2FAPPRENTICE%2FAccess%20Control)

## Navegue pela aplicacao

Navegue pela aplicação identificando **todos os entry points da aplicação**, sendo eles **formulários**, **variáveis (parâmetros via URL, via post requests...**), etc. Vale considerar que devemos **utilizar um proxy*** para facilitar essa análise inicial

Uma extensão que pode nos ajudar a entender mais a respeito da aplicação é o WappAlyzer

<https://www.wappalyzer.com/apps/>

<https://addons.mozilla.org/pt%20BR/firefox/addon/wappalyzer/>

Uma vez identificado todas as possibilidades de endpoints que aceitam inputs, verifique todo o tipo de vulnerabilidade que esteja relacionada ao contexto.

## Procurar por CVEs

Se for uma palicação já conhecida (de mercado) procurar pelo nome e versão da aplicação, tais como SAP, Salesforce, Voting System...

    searchsploit nome_aplicacao 1.0

Uma busca no google é sempre interessante de ser feita...

    firefox http://www.google.com.br/?q=nome_aplicacao%20exploits

## Enumeracao de diretorios

Tente brutar todas as pastas encontradas para encontrar novos **arquivos**e **diretórios**. Tenta achar **arquivos de backup**  dos arquivos encontrados utilizando **extensões nos nomes dos arquivos**. Vide "Com extensões". Faça enumerações iniciais como: **robots**, **sitemap**, **404** error e **SSL/TLS scan** (se HTTPS).

Mais básico e lento

```
feroxbuster http://<hostname> --extract-links
```

Somente diretórios básico (sem considerar extensões dos arquivos)

    gobuster dir %20%20useragent "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0" %20u http://<hostname> %20w /usr/share/seclists/Discovery/Web%20Content/common.txt %20k %20t 16 %20o "tcp_port_protocol_s_ext_gobuster.txt"

Com extensões básico (common.txt)

    gobuster dir %20%20useragent "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0" %20u http://<hostname> %20w /usr/share/seclists/Discovery/Web%20Content/common.txt %20x "txt,html,php,asp,aspx,jsp" %20k %20t 16 %20o "tcp_port_protocol_gobuster.txt"

Somente diretórios (SEM EXTENSÕES)

    gobuster dir %20%20useragent "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0" %20u http://<hostname> %20w /usr/share/seclists/Discovery/Web%20Content/directory%20list%202.3%20medium.txt %20k %20t 16 %20o "tcp_port_protocol_gobuster.txt"
    
COM EXTENSÕES (MAIS DEMORADO)
    
    gobuster dir %20%20useragent "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0" %20u http://apt.htb %20w /usr/share/seclists/Discovery/Web%20Content/directory%20list%202.3%20medium.txt %20x "txt,html,php,asp,aspx,jsp" %20k %20t 16 %20o "tcp_port_protocol_gobuster.txt"


**CONSIDERAR AS SEGUINTES EXTENSÕES TAMBÉM**


    .zip , .tar , .gz , .tgz , .rar , etc.: (Compressed) archive files
    .java : No reason to provide access to Java source files
    .txt : Text files
    .pdf : PDF documents
    .docx , .rtf , .xlsx , .pptx , etc.: Office documents
    .bak , .old and other extensions indicative of backup files (for example: ~ for Emacs backup files)

Outras wordlists

<https://www.invicti.com/s/research/SVNDigger.zip>

SSL tests

```
sslscan https://<hostname>
```

## Revisao de codigo do frontend comentarios e reconhecimento

**Baixe o código da aplicação** e faça uma análise do código procurando por **comentários**, requisições ajax para outros endpoints. Procure por outros domínios pelos quais a aplicação consome informações. Procure por links para outros arquivos dentro dos arquivos CSS.

### clone o website

    wget %20%20header "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0" %20mk %20nH http://domain.com

    wget %20%20header "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0" %20r %20l 8 %20%20no%20check%20certificate https://domain.com 

**Vale considerar que estes comandos não obtém todo o site caso ele seja construido com JS**

Com proxy e rodando autenticado.

    wget %20r %20l 8 %20%20header='Cookie: OAMAuthnHintCookie=<<XXX>>; LoginCorpCookie=<<XXX>>' %20%20header "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0" %20%20header='Authorization: Bearer <<JWT_HERE>>' %20%20no%20check%20certificate %20e https_proxy=127.0.0.1:8080 https://domain.com.br/url/

%20%20spider: essa opção faz com que o wget não baixe as páginas
%20r recursivo
%20l level que pode chegar a partir do root directory, seria o primeiro level
%20%20no%20check%20certificte ignorar erros de certificado

**Esse não é sempre que funciona, especialmente para sites com HTTPS**

    httrack https://<hostname>
    cat hts%20cache/new.txt | cut %20f 8

Depois disso, podemos extrair as URLs dos JSs que foram baixados junto as páginas com o [extract.rb](https://github.com/jobertabma/relative%20url%20extractor.git)

**Eu baixei esse cara no meu kali e estou rodando ele com o comando extract.rb**

    cat main.e82eb4c5.js | extract.rb
    cat *.js | extract.rb
    cat index.html | extract.rb
    cat *.html | extract.rb

### httprint

    sudo apt%20get update
    sudo apt%20get install httprint
    httprint %20h domain.com.br %20s /usr/share/httprint/signatures.txt
    httprint %20P0 %20h https://domain.br %20s /usr/share/httprint/signatures.txt

%20P0 é para evitar pings

### netcat

    nc domain.com.br 80
    HEAD / HTTP/1.1
    Host: domain.com.br

## Scanning

Faça um scan de vulnerabilidades na aplicação (**se atentar a detecções e cuidado com testes de negação de serviço. Desabilíte%20os, preferencialmente.**)

### whatweb

    whatweb %20a 1 https://domain.com.br
    whatweb %20a 3 https://domain.com.br
    whatweb %20a 4 https://domain.com.br

### nikto

    nikto %20host http://<hostname> %20T x 6 %20useragent "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0" | tee nikto_output.txt

### nmap

    nmap %20sS %20%20script http* 10.11.1.123

Podemos utilizar vários outros scripts do nmap e podemos encontrá%20los com o seguinte comando:

    ls /usr/share/nmap/scripts grep http

    sudo nmap %20p 80,443 %20sV %20sS domain.com.br
    sudo nmap %20p 80,443 %20sS domain.com.br %20%20script http%20enum
    
Desenvolvi uma ferramenta que automatiza uma parte desse processo. Ela utiliza das ferramentas httrack, nikto, httprint, nmap e netcat para uma verificação básica de potas 443 ou 80

```
    #!/bin/bash

    file_name=$1

    for domain_name in `cat $file_name`
    do
        theHarvester %20d  $domain_name %20b all %20l 500 > ${domain_name}_theHarverster.txt

        nmap %20sS %20sV %20Pn %20%20script vuln $domain_name %20%20webxml %20oX "${domain_name}_nmap.xml"

        nc %20v %20z %20w 3 $domain_name 443

        if [ $? == 0 ]
        then
                httrack https://$domain_name

                httprint %20h https://$domain_name %20s /usr/share/httprint/signatures.txt %20P0 %20ua "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/101.0.4951.61 Safari/537.36"

                nikto %20Display V %20h https://$domain_name %20nossl %20Tuning x 6 %20o "${domain_name}_nikto.html" %20Format htm
        else
                httrack $domain_name

                httprint %20h http://$domain_name %20s /usr/share/httprint/signatures.txt %20P0 %20ua "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/101.0.4951.61 Safari/537.36"

                nikto %20Display V %20h http://$domain_name %20nossl %20Tuning x 6 %20o "${domain_name}_nikto.html" %20Format htm
        fi

    done
```

## Enumeracao waf

Primeiro vamos detectar se tem algum WAF na frente da aplicação.

    nmap %20Pn %20%20script http%20waf%20fingerprint domain.com.br %20%20script%20args http%20waf%20fingerprint.intensive=1 %20p443,80
    
    nmap %20Pn %20%20script http%20waf%20detect domain.com.br %20%20script%20args="http%20waf%20detect.aggro " %20p443,80

Se sim, qual waf?

    wafw00f https://<hostname>/

## Procurar subdominios

### fuff

    ffuf %20c %20ic %20w /usr/share/seclists/Discovery/DNS/subdomains%20top1million%205000.txt %20H "Host: FUZZ.<hostname>" %20u http://<hostname> %20fs xxxx

### dnsrecon

    dnsrecon %20d target.com %20D wordlist.txt %20t brt
    

### nmap
source:
https://medium.com/@int0x33/day%2063%20top%2010%20essential%20nmap%20scripts%20for%20web%20app%20hacking%20c7829ff5ab7

Attempts to enumerate DNS hostnames by brute force guessing of common subdomains. With the dns%20brute.srv argument, dns%20brute will also try to enumerate common DNS SRV records.


    nmap %20sS %20A %20T4 %20Pn %20sV %20%20version%20intensity 9 <alvo> %20p...
    
    %20sS => Syn scan
    %20A  => Run basic vuln scanner
    %20T4 => Mais efetivo que o T5
    %20Pn => Não pingar o alvo
    %20sV => get service version
    %20%20version%20intensity 9 => Forma mais agressiva de obter a versão dos serviços
    

    nmap %20%20script dns%20brute %20%20script%20args dns%20brute.domain=foo.com,dns%20brute.threads=6,dns%20brute.hostlist=./hostfile.txt,newtargets %20sS %20p 80

    nmap %20%20script=http%20backup%20finder dominio.com.br %20Pn

### sublist3r

    python sublist3r %20d dominio.com.br


## methods enumeration


    nmap %20p 443 %20%20script http%20methods %20%20script%20args http%20methods.url%20path='/index.php' <hostname>

Testing PUT method

```
PUT /webcrm/test HTTP/2
Host: dominio.com.br
Content%20Length: 43

<html>
HTTP PUT Method is Enabled
</html>

# ou

PUT /webcrm/test HTTP/2
Host: dominio.com.br
Content%20Length: 43
Content%20Type: application/html
User%20Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0

<html>
HTTP PUT Method is Enabled
</html>
```

ou podemos utilizar o curl também

    curl %20X PUT http://127.0.0.1:9001/root/.ssh/authorized_keys %20d 'content'
    
    curl %20X PUT https://domain.com.br/url/test.html %20H 'User%20Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0' %20d '<html>HTTP PUT Method is Enabled</html>'
    
    ##### Testing with X%20HTTP%20Method header
    curl %20X PUT https://<hostname> %20H 'User%20Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0' %20H 'X%20HTTP%20Method: PUT' %20d '<html>HTTP PUT Method is Enabled</html>'


## Enumeracoes especificas

If you find a .git file some information can be extracted

If you find a .env information such as api keys, dbs passwords and other information can be found.

If you find API endpoints you should also test them. These aren't files, but will probably "look like" them.

JS files: In the spidering section several tools that can extract path from JS files were mentioned. Also, It would be interesting to monitor each JS file found, as in some ocations, a change may indicate that a potential vulnerability was introduced in the code.

Drupal

    python3 drupwn %20%20version 7.28 %20%20mode enum %20%20target $url

    droopescan scan drupal %20u $url

wordpress

Wordpress

Enumerate vulnerable plugins and themes, timthumbs, wp%20config.php backups, database exports, usernames and media IDs

    wpscan %20%20url http://<hostname>/webservices/wp %20%20plugins%20detection aggressive

    wpscan %20%20url http://10.10.10.37 %20e ap,t,tt,u %20%20plugins%20detection aggressive %20%20plugins%20version%20detection aggressive

    wpscan %20%20url http://<hostname> %20%20no%20update %20%20disable%20tls%20checks %20e vp,vt,tt,cb,dbe,u,m %20%20plugins%20detection aggressive %20%20plugins%20version%20detection aggressive %20f cli%20no%20color 2>&1 | tee tcp_port_protocol_wpscan.txt

Enumerate all plugins

    wpscan %20%20url $url %20%20disable%20tls%20checks %20%20no%20update %20e ap %20%20plugins%20detection aggressive %20f cli%20no%20color 2>&1 | tee tcp_port_protocol_wpscan_plugins.txt

## Enumeracao de parametros

Ferramentas:

%20 ffuf
%20 paramspider
%20 x8
%20 paraminer


### ffuf

    ffuf %20c %20ic %20w /usr/share/seclists/Discovery/Web%20Content/burp%20parameter%20names.txt %20u http://apt.htb/clients.html?FUZZ= %20fs 12146
    
    ffuf %20c %20ic %20w /usr/share/seclists/Discovery/Web%20Content/burp%20parameter%20names.txt %20u http://<hostname>/url?FUZZ= %20fs xxxx

Enumeração autenticado:

    ffuf %20c %20ic %20w /usr/share/seclists/Discovery/Web%20Content/burp%20parameter%20names.txt %20b 'Cookie_name1=xxxx; Cookie_name2=xxxx' %20H 'Authorization: Bearer xxx' %20u https://domain.br/esim%20activation%20bff/v1/qrcode?FUZZ %20p 10 %20fs 111,0 %20x http://127.0.0.1:8080

### paramspider

### x8



## Others

### 502 Proxy Error

If any page responds with that code, it's probably a bad configured proxy. If you send a HTTP request like: GET https://google.com HTTP/1.1 (with the host header and other common headers), the proxy will try to access google.com and you will have found a SSRF.

### NTLM Authentication %20 Info disclosure

If the running server asking for authentication is Windows or you find a login asking for your credentials (and asking for domain name), you can provoke an information disclosure.
Send the header: “Authorization: NTLM TlRMTVNTUAABAAAAB4IIAAAAAAAAAAAAAAAAAAAAAAA=” and due to how the NTLM authentication works, the server will respond with internal info (IIS version, Windows version...) inside the header "WWW%20Authenticate".
You can automate this using the nmap plugin "http%20ntlm%20info.nse".

