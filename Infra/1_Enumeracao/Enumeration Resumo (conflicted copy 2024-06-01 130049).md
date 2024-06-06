Resumo Enumeracao
========================

**Checklist**

- [ ] [**Enumeração de serviços e vulnerabildiades**](#enumeracao%20de%20servicos)
- [ ] [**Enumeração HTTP e HTTPS**](#enumeracao%20http%20e%20https)
    - [ ] Navegação manual/Examinar o conteúdo da página (comentários, javascript, links escondidos)
    - [ ] A aplicação possui CVEs?
    - [ ] Verificar os cabeçalhos de resposta do servidor
    - [ ] [Enumerar URLs/arquivos/endpoints/consoles administrativas desconhecidos](#URL%20brute%20force)
    - [ ] [Scanning](#web-scan-nikto)
    - [ ] [Enumeração de usuários e brute force em formulários de login](#enumeracao-de-usuario-e-brute-force)
- [ ] [**Enumeração SMB e RPC**](#enumeracao-smb-e-rpc)
    - [ ]  [Null Session](#null-session)
    - [ ]  [Guest Session](#guest-session)
    - [ ]  [Acesso de escrita](#acesso-de-escrita)
- [ ] [**Enumeração LDAP**](#enumeracao-ldap)
- [ ] [**Enumeração DNS**](#enumracao-dns)
- [ ] [**Enumeração SMTP**](#enumeracao-smtp)
- [ ] [**Enumeração SNMP**](#enumeracao-snmp)
- [ ] [**Enumeração NFS**](#enumeracao-nfs)
- [ ] [**Enumeração FTP**](enumeracao-ftp)
- [ ] [**Enumeração Active Directory**](#active-directory)
    - [ ] [Obter nome domínio](#obter-nome-dominio)
    - [ ] [Enumerar usuários](#enumerar-usuarios)
    - [ ] [**AsRep Roasting**](#asrep-roasting)


## Enumeracao de servicos

Nmap basico

    sudo nmap_enum <hostname> | tee nmap_output.txt

Vide [Referências](#referencias) Para obter o script nmap_enum.

Scan UDP básico
    
    sudo nmap -sU -p 53,161,111,137,139,500,2049 -Pn <hostname> | tee nmap_udp_output.txt

Full TCP scan

    sudo nmap -p- -Pn -T5 -n <ip> | tee nmap_fullportstcp_output.txt

Better performance

    sudo nmap -p- --max-retries 0 -Pn -T5 -n <ip> | tee nmap_fullportstcp_output2.txt

Scan de vulnerabilidade nmap

    sudo nmap -sS --script vuln -n -T5 -Pn <ip> -p <ports> | tee nmap_vulns.txt

### Referência em vídeo

![type:video](https://youtube.com/embed/AJfDbI2bRRA)

[Script nmap_enum](https://www.youtube.com/watch?v=AJfDbI2bRRA&t=0s)  
[UDP scan somente portas de serviços exploráveis na OSCP](https://www.youtube.com/watch?v=AJfDbI2bRRA&t=20s)  
[TCP scan full (todas as portas)](https://www.youtube.com/watch?v=AJfDbI2bRRA&t=30s)  
[Scan de vulnerabilidades com nmap](https://www.youtube.com/watch?v=AJfDbI2bRRA&t=46s)  

## Enumeracao HTTP e HTTPS

### URL brute force

URL brute force common without extensions

    gobuster dir --useragent "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0" -u http://<hostname> -w /usr/share/seclists/Discovery/Web-Content/common.txt -k -t 16 -o "tcp_port_protocol_s_ext_gobuster.txt"

URL brute force common WITH extensions

    feroxbuster -w /usr/share/seclists/Discovery/Web-Content/common.txt -x "txt,html,asp,aspx" -u http://<hostname>

Caso precise filtrar:

    --filter-status 500,403
    --filter-code
    --filter-words

URL brute force medium WITHOUT extensions

    gobuster dir --useragent "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0" -u http://<hostname> -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -k -t 16 -o "tcp_port_protocol_s_ext_medium_gobuster.txt"

URL brute force medium w/ extensions fuff

    ffuf -u http://<hostname>/FUZZ -ic -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -e ".txt,.html,.php,.asp,.aspx,.jsp" -fs <filtros> -H "User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0" -x http://127.0.0.1:8080

### Subdomain enumeration

    ffuf -c -ic -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -H "Host: FUZZ.<hostname>" -H "User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0" -u http://<hostname> -fs xxx

### Web scan nikto

    nikto -host http://<hostname> -T x 6 -useragent "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0" | tee nikto_output.txt

### Extrair links do site

Podemos baixar o conteúdo do site para a máquina e fazer a extração das URLs com os seguintes comandos (lembrando que a ferramenta possui limitações para fazer isso de maneira autenticada e também não funciona proxy para chamadas em HTTPS):

    httrack https://atsserver.acute.local
    cd hts-cache
    cat new.txt | cut -f 8

Dentro do diretório de ferramentas de web, eu coloquei uma ferramenta que fiz uma pequena alteração para extrair as URLs do site (sem estar autenticado):

    /home/acosta/work/Area_de_trabalho/tools/web/2_enumeration/urlExtract -r 4 https://<hostname>

ou

    /home/acosta/go/bin/urlExtract -r 4 https://<hostname>

ou somente

    urlExtract -r 4 https://<hostname>

### sslscan

Dessa forma podemos obter subdomínios identificados em certificados:

    sslscan https://<hostname>

### Enumeracao de usuario e brute force

**OBS: Para alterar o User-Agent deve ser informado o cabeçalho via H:.... Segue exemplo abaixo. Vale ressaltar que para o basic authentication essa feature não é suportada e tem que ser feito ajustes via proxy (no burp, por exemplo)**

    hydra -l root@locahost -P /usr/share/wordlist/rockyou.txt 10.11.1.39 http-post-form "/otrs/index.pl:H=User-Agent\: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv\:88.0) Gecko/20100101 Firefox/88.0':Action=Login&RequestedURL=&Lang=en&TimeOffset=300&User=root@localhost&Password=^PASS^:Login failed!"

#### get basic authentication
    hydra -C /usr/share/seclists/Passwords/Default-Credentials/tomcat-betterdefaultpasslist.txt 10.11.1.209 -s 8080 http-get /manager/html

onde -s é a porta
http-get é o método a ser utilizado
/manager/html é o diretório pelo qual estamos tentando nos autenticar
e - C colon separated "login:pass" format, instead of -L/-P options

COM SSL

    proxychains hydra -L usernames.txt -P passwords.txt 10.10.10.7 -s 443 -S http-get /admin/config.php

#### post (Form authentication)

    hydra -l root@locahost -P /usr/share/wordlist/rockyou.txt 10.11.1.39 http-post-form "/otrs/index.pl:Action=Login&RequestedURL=&Lang=en&TimeOffset=300&User=root@localhost&Password=^PASS^:Login failed!"

### Referência em vídeo

![type:video](https://youtube.com/embed/xuJCzHr6ZWY)

[Enumeração diretórios via URL escondidas com ffuf](https://www.youtube.com/watch?v=xuJCzHr6ZWY&t=205s)  
[Enumeração de arquivos escondidos via URL com ffuf](https://www.youtube.com/watch?v=xuJCzHr6ZWY&t=410s)  
[Inspecionando o código da página](https://www.youtube.com/watch?v=xuJCzHr6ZWY&t=660s)  
[Procura por CVE da aplicação](https://www.youtube.com/watch?v=xuJCzHr6ZWY&t=846s)  
[Verificar cabeçalhos da aplicação](https://www.youtube.com/watch?v=xuJCzHr6ZWY&t=920s)  
[Scan de vulnerabilidade com nikto](https://www.youtube.com/watch?v=xuJCzHr6ZWY&t=1000s)  
[Brute Force com hydra par autenticação HTTP básica](https://www.youtube.com/watch?v=xuJCzHr6ZWY&t=1110s)  
[Brute Force com hydra par autenticação HTTP básica com SSL/TLS](https://www.youtube.com/watch?v=xuJCzHr6ZWY&t=1198s)  
[Brute force via requisição POST com Burpsuite](https://www.youtube.com/watch?v=xuJCzHr6ZWY&t=1258s)  
[Brute force via requisição POST com Hydra](https://www.youtube.com/watch?v=xuJCzHr6ZWY&t=1339s)  

## Enumeração SMB e RPC


### Null session

SMB Null session 1

    smbclient -L //<hostname>

SMB Null session 2
    
    smbclient -L //<hostname> -U '' -N

SMB Null session 3
    
    smbclient -L //<hostname> -U ''

    crackmapexec smb <hostname> -u '' -p '' --shares

    sudo nmap -p 445,139 -Pn -T5 10.10.10.179 --script smb-enum-shares

### Guest session

    crackmapexec smb <hostname> -u 'guest' -p '' --shares

### Acesso de escrita

Tente escrever algum arquivo no compartilhamento. Funcionando, utilize [essa referência](../2_Exploracao/SMB.md#acesso-de-escrita) para saber como proceder:
Improvável, mas vale o teste

    impacket-samrdump <hostname>

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

## Enumeração LDAP

    ldapsearch -x -H ldap://<hostname> -D '' -w '' -b "DC=<domain_name>,DC=<tld>"

```
ldapsearch -x -H ldap://10.10.10.179 -s base
```

## Enumeração DNS

Dig
    
    dig axfr <hostname> @<ip_dns_server>

DNSEnum
    
    dnsenum -dnsserver <ip_dns_server> <hostname>

**Aqui o dnsenum utiliza do /etc/resolv.conf para fazer transferência de zona**

dnsrecon
    
    dnsrecon -d <hostname> -n <ip_dns_server>

### Referência em vídeo

![type:video](https://youtube.com/embed/hDrpjycOMn0)

[Identificando servidores DNS com nmap](https://www.youtube.com/watch?v=hDrpjycOMn0&t=65s)  
[Configurando o DNS correto no seu Kali Linux no resolv.conf](https://www.youtube.com/watch?v=hDrpjycOMn0&t=183s)  
[Enumeração manual com o comando host dos tipos a,mx,any,axfr (transferência de zona) e txt](https://www.youtube.com/watch?v=hDrpjycOMn0&t=238s)  
[Enumeração automatizada com dnsenum](https://www.youtube.com/watch?v=hDrpjycOMn0&t=785s)  
[Enumeração automatizada com dnsrecon](https://www.youtube.com/watch?v=hDrpjycOMn0&t=895s)  
[Enumeração automatizada com dig](https://www.youtube.com/watch?v=hDrpjycOMn0&t=1125s)

## Enumeração SMTP

    nmap -sS -p 25 -n -sV --version-10.11.1.25

    
    nc -nv 10.11.1.217 25
    VRFY root
    VRFY idontexist
    
    VRFY <username>
    
    user <username>
    pass <password>
    list
    
    retr 1
    retr 2

Enumeracao de usuarios

    smtp-user-enum -M EXPN -U usernames.txt -t <hostname>    
    smtp-user-enum -M VRFY -U usernames.txt -t <hostname>
    smtp-user-enum -M RCPT -U usernames.txt -t <hostname>
    smtp-user-enum -M RCPT -D dominio.com.br -U usernames.txt -t <hostname>

Envio de email com conteudo malicioso anexado (attachments). Vide [esta referencia](../Malware/OfficeDocs.md)

    sendEmail -t hr@dominio.com.br -f atacante@teste.com.br -a arquivo_malicioso.doc -s 10.10.10.10 


## Enumeração SNMP

Bruteforce de nomes de comunidades

    onesixtyone <hostname> -c communities.txt

Checa acesso de escrita

    snmp-check -w -c public <hostname>

Enumeração snmp com nmap
    
    sudo nmap -sU -p 161 --script "snmp*" <hostname>

snmpwalk
    
    snmpwalk -c public -v 2c <hostname>

**Importante notar que este comando consegue obter mais informações a respeito do serviço SNMP, do servidor, inclusive enumerar usuários e arquivos do sistema***

    snmpwalk -c public -v2c <hostname> . | tee /tmp/snmpwalk_full.txt

    snmpwalk -c public -v1 <hostname> . | tee /tmp/snmpwalk_full.txt

Vale considerar que temos que observar as pastas que conseguirmos ver de webservers, por exemplo, além de credenciais em texto claro em linha de comando, também. Foram os cenários que observei no hack the box

snmpbulkwalk e snmp_process_list.py

(Esses últimos comandos não foram documentados em vídeo, dado redundância dele com os demais comandos já mostrados)

    sudo apt update
    sudo apt install snmp-mibs-downloader

    snmpbulkwalk -c public -v2c <hostname> | tee snmpbulk.txt

    python snmp_process_list.py snmpbulk.txt

### Referência em vídeo

![type:video](https://youtube.com/embed/cPjZTqfkHbc)

[Identificação do serviço SNMP](https://www.youtube.com/watch?v=cPjZTqfkHbc&t=38s)  
[Enumeração SNMP utilizando nmap](https://www.youtube.com/watch?v=cPjZTqfkHbc&t=84s)  
[Descobrir o nome da community com onesixtyone e wordlists utililzadas](https://www.youtube.com/watch?v=cPjZTqfkHbc&t=139s)  
[Enumerando com snmpwalk](https://www.youtube.com/watch?v=cPjZTqfkHbc&t=277s)  
[Enumerando com snmp-check](https://www.youtube.com/watch?v=cPjZTqfkHbc&t=381s)  

## Enumeração NFS

Enumeração com nmap do NFS

    nmap -Pn -sV -p 111,2049 --script="banner,(rpcinfo or nfs*) and not (brute or broadcast or dos or external or fuzzer)" -oN "tcp_111_2049_nfs_nmap.txt" <hostname>

Mostra informações de compartilhamento da máquina alvo

    showmount -e

Montando o compartilhamento na máquina

    sudo mount -o rw,vers=2 <hostname>:/home /mnt

No entanto, no momento de montar o compartilhamento é necessário uma configuração de usuário para exploração desta falha. Vide [esta referência](../2_Exploracao/NFS.md)

## Enumeração FTP

    ftp -a <hostname>

ou para transferência de arquivos em modo ativo:

    ftp -a -A <hostname>

**Se atentar ao fato de possíveis arquivos escondidos**

    ls -al

## Active Directory

Primeiro precisamos de informações básicas a respeito do domínio ou quem é o DC (Domain Controller) para sabermos quem devemos ter como alvo para extração de hashes e afins...

### IP dos domain controllers

linux

    nslookup -type=srv _ldap._tcp.dc._msdcs.<dominio.com.br>
    nslookup -type=srv _kerberos._tcp.<dominio.com.br>
    nslookup -type=srv _kpasswd._tcp.<dominio.com.br>
    nslookup -type=srv _ldap._tcp.<dominio.com.br>

windows

    nslookup
    set type=all
    _ldap._tcp.dc._msdcs.<dominio.com.br>

Outra opção seria:

    nltest /dclist:<dominio.local>
 
Vale considerar que este útlimo comando só funciona se a máquina já está no domínio.

### Obter nome domínio

**SEM ACESSO INICIAL NA MÁQUINA**

No Linux

    enum4linux -a <hostname_dc>

Utilizar também

    crackmapexec smb <hostname_dc>


**COM ACESSO NA MÁQUINA**

No Windows

    [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()

ou

*Vale considerar que este comando só funciona se a máquina já está no domínio*

    whoami

### Enumerar usuários

**SEM ACESSO INICIAL NA MÁQUINA**

    kerbrute userenum -d dominio.local /usr/share/seclists/Usernames/xato-net-10-million-usernames.txt

ou

    rpcclient -U <domain>/<usuario> <hostname_dc>
    enumdomusers
    srvinfo
    querydominfo
    enumdomgroups
    querygroup 0x200

    netshareenum
    netshareenumall

ou 

    enum4linux -a <hostname_dc>

Via SMB

    crackmapexec smb <hostname> --users

Ldapsearch

    ldapsearch -x -H ldap://<hostname> -D '' -w '' -b "DC=<domain_name>,DC=<tld>"

**COM ACESSO NA MÁQUINA**

     net user
     net user /domain
     
     net user username /domain


### AsRep Roasting

    impacket-GetNPUsers -usersfile usernames.txt domain.local/ --dc-host <hostname_dc>

Com base nessa enumeração, provável que já teremos alguma credencial válida no domínio, então seguimos para os [próximos passos aqui.](../2_Exploracao/Active%20Directory.md)

**Observação abaixo, caso tenha uma máquina fora do domínio**

    runas /netonly /user:domain.br\user powershell
(será solicitado credencial e pode ser informado uma credencial inválida que o comando funcionará, mas por motivos óbvios, quaisquer comando que utilizemos não se autenticará na rede)

## Outros

1) Enumeração de domínios via certificado com sslscan, por exemplo:

    sslscan <hostname>

2) Verificar as credenciais padrão das páginas web (sistemas já conhecidos)
3) Verificar as credenciais (as default e as que conseguimos via enumeração) obtidas e realizar o bruteforce em TODOS os outros SERVIÇOS encontrados na máquina


## Referências

[script nmap_enum](https://github.com/segurancadecomputadores/InfraHacking/blob/main/Tools/Scripts/nmap_enum.md)

Basta copiar para um arquivo em um diretório da sua máquina e relacioná-lo no /usr/bin com link simbólico, por exemplo:

    sudo ln -s /home/user/Downloads/nmap_enum /usr/bin/nmap_enum
