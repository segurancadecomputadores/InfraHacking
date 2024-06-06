Resumo Enumeracao
========================

**Checklist**

- [ ] [**Enumeração de serviços e vulnerabilidades**](#enumeracao%20de%20servicos)
- [ ] [**Enumeração HTTP e HTTPS**](#enumeracao%20http%20e%20https)
    - [ ] [Navegação manual/Examinar o conteúdo da página (comentários, javascript, links escondidos)](#analisar%20o%20site)
    - [ ] [A aplicação possui CVEs?](#cves)
    - [ ] [Verificar os cabeçalhos de resposta do servidor](#verifica%20cabecalhos)
    - [ ] [Enumerar URLs/arquivos/endpoints/consoles administrativas desconhecidos](#url%20bruteforce)
    - [ ] [Enumerar subdomínios e virtual hosts](#enumerar%20subdominios%20e%20virtual%20hosts)
    - [ ] [Scanning](#web%20scan%20nikto)
    - [ ] [Informações de certificado](#sslscan)
    - [ ] [Enumeração de usuários e brute force em formulários de login](#enumeracao%20de%20usuario%20e%20brute%20force)
- [ ] [**Enumeração SMB e RPC**](#enumeracao%20smb%20e%20rpc)
    - [ ]  [Null Session](#null%20session)
    - [ ]  [Guest Session](#guest%20session)
    - [ ]  [Acesso de escrita](#acesso%20de%20escrita)
- [ ] [**Enumeração LDAP**](#enumeracao%20ldap)
- [ ] [**Enumeração DNS**](#enumeracao%20dns)
- [ ] [**Enumeração SMTP**](#enumeracao%20smtp)
- [ ] [**Enumeração SNMP**](#enumeracao%20snmp)
- [ ] [**Enumeração NFS**](#enumeracao%20nfs)
- [ ] [**Enumeração FTP**](#enumeracao%20ftp)
- [ ] [**Enumeração Active Directory**](#active%20directory)
    - [ ] [Obter nome domínio](#obter%20nome%20dominio)
    - [ ] [Enumerar usuários](#enumerar%20usuarios)
    - [ ] [AsRep Roasting](#asrep%20roasting)
- [ ] [Outros](#outros)
- [ ] [**Referências**](#referencias)


## Enumeracao de servicos

Nmap basico

```
sudo nmap_enum <hostname> | tee nmap_output.txt
```

Vide [Referências](#referencias) Para obter o script nmap_enum.

Scan UDP básico

```
sudo nmap -sU -p 53,161,111,137,139,500,2049 -Pn <hostname> | tee nmap_udp_output.txt
```

Full TCP scan

```
sudo nmap -p- -Pn -T5 -n <ip> | tee nmap_fullportstcp_output.txt
```

Better performance

```
sudo nmap -p- --max-retries 0 -Pn -T5 -n <ip> | tee nmap_fullportstcp_output2.txt
```

Scan de vulnerabilidade nmap

```
sudo nmap -sS --script vuln -n -T5 -Pn <ip> -p <ports> | tee nmap_vulns.txt
```

### Referência em vídeo

![type:video](https://youtube.com/embed/AJfDbI2bRRA)

[Script nmap_enum](https://www.youtube.com/watch?v=AJfDbI2bRRA&t=0s)  
[UDP scan somente portas de serviços exploráveis na OSCP](https://www.youtube.com/watch?v=AJfDbI2bRRA&t=20s)  
[TCP scan full (todas as portas)](https://www.youtube.com/watch?v=AJfDbI2bRRA&t=30s)  
[Scan de vulnerabilidades com nmap](https://www.youtube.com/watch?v=AJfDbI2bRRA&t=46s)  

## Enumeracao HTTP e HTTPS

### Analisar o site

Vide [Referências](#referencias) para obter o script urlExtract.

```
urlExtract -r 4 https://<hostname>
```

Sendo -r a profundidade (em níveis de diretórios) que a ferramenta deve alcançar.
   
Analisar o código do front end baixado e verificar todas as URLs existentes no site
```
httrack http://<hostname>
cd hts-cache
cat new.txt | cut -f 8
```

## CVEs

Obter o nome da aplicação por meio da navegação ou cabeçalhos na resposta do servidor e procurar por CVEs:

```
searchsploit <versão_da_aplicação>
```
### Verifica cabecalhos

Verificar os headers com curl

```
curl -v http://<hostname> -H "User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0" -o /dev/null
```

(Passível de customização a depender do caso, como alterar o User-Agent ou alterar os métodos de consulta HTTP)
### URL bruteforce

URL brute force common sem extensões:

```
gobuster dir --useragent "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0" -u http://<hostname> -w /usr/share/seclists/Discovery/Web-Content/common.txt -k -t 16 -o "tcp_port_protocol_s_ext_gobuster.txt"
```

URL brute force common com extensões

```
feroxbuster -w /usr/share/seclists/Discovery/Web-Content/common.txt -x "txt,html,asp,aspx" -u http://<hostname>
```

Caso precise filtrar:

    --filter-status 500,403
    --filter-code
    --filter-words

URL brute force com o dicionário médio sem extensões

```
gobuster dir --useragent "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0" -u http://<hostname> -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -k -t 16 -o "tcp_port_protocol_s_ext_medium_gobuster.txt"
```

URL brute force medium com extensões utilizando ffuf

```
ffuf -u http://<hostname>/FUZZ -ic -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -e ".txt,.html,.php,.asp,.aspx,.jsp" -fs <filtros> -H "User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0" -x http://127.0.0.1:8080
```

### Enumerar subdominios e virtual hosts

```
 ffuf -c -ic -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -H "Host: FUZZ.<hostname>" -H "User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0" -u http://<hostname> -fs xxx
```

### Web scan nikto

```
nikto -host http://<hostname> -T x 6 -useragent "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0" | tee nikto_output.txt
```
### sslscan

Dessa forma podemos obter subdomínios identificados em certificados:

```
sslscan https://<hostname>
```

### Enumeracao de usuario e brute force

**OBS: Para alterar o User-Agent deve ser informado o cabeçalho via H:.... Segue exemplo abaixo. Vale ressaltar que para o basic authentication essa feature não é suportada e tem que ser feito ajustes via proxy (no burp, por exemplo)**

**Verificar as credenciais padrão das páginas web (sistemas já conhecidos)**

```
hydra -l root@locahost -P /usr/share/wordlist/rockyou.txt 10.11.1.39 http-post-form "/otrs/index.pl:H=User-Agent\: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv\:88.0) Gecko/20100101 Firefox/88.0':Action=Login&RequestedURL=&Lang=en&TimeOffset=300&User=root@localhost&Password=^PASS^:Login failed!"
```

#### get basic authentication

```
hydra -C /usr/share/seclists/Passwords/Default-Credentials/tomcat-betterdefaultpasslist.txt 10.11.1.209 -s 8080 http-get /manager/html
```

onde -s é a porta
http-get é o método a ser utilizado
/manager/html é o diretório pelo qual estamos tentando nos autenticar
e - C colon separated "login:pass" format, instead of -L/-P options

COM SSL

```
proxychains hydra -L usernames.txt -P passwords.txt 10.10.10.7 -s 443 -S http-get /admin/config.php
```

#### post (Form authentication)

```
hydra -l root@locahost -P /usr/share/wordlist/rockyou.txt 10.11.1.39 http-post-form "/otrs/index.pl:Action=Login&RequestedURL=&Lang=en&TimeOffset=300&User=root@localhost&Password=^PASS^:Login failed!"
```

### Referencia em video

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

## Enumeracao SMB e RPC


### Null session

Embora pareça ser redundante os testes, o comportamento de cada comando abaixo difere e pode trazer resultados diferentes:

SMB Null session 1

```
smbclient -L //<hostname>
```

SMB Null session 2

```
smbclient -L //<hostname> -U '' -N
```

SMB Null session 3    

```
smbclient -L //<hostname> -U ''
```

```
netexec smb <hostname> -u '' -p '' --shares
```

```
sudo nmap -p 445,139 -Pn -T5 10.10.10.179 --script smb-enum-shares
```

### Guest session

```
netexec smb <hostname> -u 'guest' -p '' --shares
```

### Acesso de escrita

Tente escrever algum arquivo no compartilhamento. Funcionando, utilize [essa referência](../2_Exploracao/SMB.md#obter%20hashes%20de%20senhas) para saber como proceder:
Improvável, mas vale o teste

```
impacket-samrdump <hostname>
```

RPC Null session
    
```
rpcclient -U '' -N <hostname>
    srvinfo
    enumdomusers
    getdompwinfo
    querydominfo
    netshareenum
    netshareenumall
```

Enum4linux
    
```
enum4linux -a <hostname>
```

## Enumeracao LDAP

```
ldapsearch -x -H ldap://<hostname> -D '' -w '' -b "DC=<domain_name>,DC=<tld>"
```

```
ldapsearch -x -H ldap://10.10.10.179 -s base
```

## Enumeracao DNS

Transferência de zona com dig
    
```
dig axfr <hostname> @<ip_dns_server>
```

DNSEnum
    
```
dnsenum -dnsserver <ip_dns_server> <hostname>
```

**Aqui o dnsenum utiliza do /etc/resolv.conf para fazer transferência de zona**

dnsrecon

```
dnsrecon -d <hostname> -n <ip_dns_server>
```

### Referência em vídeo

![type:video](https://youtube.com/embed/hDrpjycOMn0)

[Identificando servidores DNS com nmap](https://www.youtube.com/watch?v=hDrpjycOMn0&t=65s)  
[Configurando o DNS correto no seu Kali Linux no resolv.conf](https://www.youtube.com/watch?v=hDrpjycOMn0&t=183s)  
[Enumeração manual com o comando host dos tipos a,mx,any,axfr (transferência de zona) e txt](https://www.youtube.com/watch?v=hDrpjycOMn0&t=238s)  
[Enumeração automatizada com dnsenum](https://www.youtube.com/watch?v=hDrpjycOMn0&t=785s)  
[Enumeração automatizada com dnsrecon](https://www.youtube.com/watch?v=hDrpjycOMn0&t=895s)  
[Enumeração automatizada com dig](https://www.youtube.com/watch?v=hDrpjycOMn0&t=1125s)

## Enumeracao SMTP

```
nmap -sS -p 25 -n -sV --version-10.11.1.25
```

    
```
nc -nv 10.11.1.217 25
```

Geralmente funciona melhor com telnet

```
telnet 10.11.1.217 25
```
```
VRFY root
VRFY idontexist
VRFY <username>
user <username>
pass <password>
list

retr 1
retr 2
```


Enumeracao de usuarios

```
smtp-user-enum -M EXPN -U usernames.txt -t <hostname>    
smtp-user-enum -M VRFY -U usernames.txt -t <hostname>
smtp-user-enum -M RCPT -U usernames.txt -t <hostname>
smtp-user-enum -M RCPT -D dominio.com.br -U usernames.txt -t <hostname>
```

Envio de email com conteudo malicioso anexado (attachments). Vide [esta referencia](../Malware/OfficeDocs.md)

```
sendEmail -t hr@dominio.com.br -f atacante@teste.com.br -a arquivo_malicioso.doc -s 10.10.10.10 
```


## Enumeracao SNMP

Bruteforce de nomes de comunidades

```
onesixtyone <hostname> -c communities.txt
```

Checa acesso de escrita

```
snmp-check -w -c public <hostname>
```

Enumeração snmp com nmap

```
sudo nmap -sU -p 161 --script "snmp*" <hostname>
```

snmpwalk

```
snmpwalk -c public -v 2c <hostname>
```

**Importante notar que este comando consegue obter mais informações a respeito do serviço SNMP, do servidor, inclusive enumerar usuários e arquivos do sistema***

```
snmpwalk -c public -v2c <hostname> . | tee /tmp/snmpwalk_full.txt
```

```
snmpwalk -c public -v1 <hostname> . | tee /tmp/snmpwalk_full.txt
```

Vale considerar que temos que observar as pastas que conseguirmos ver de webservers, por exemplo, além de credenciais em texto claro em linha de comando, também. Foram os cenários que observei no hack the box. Vale considerar que os comandos anterior já contemplam os resultados dos comandos abaixo, mas deixo como alternativa.

snmpbulkwalk e snmp_process_list.py

(Esses últimos comandos não foram documentados em vídeo, dado redundância dele com os demais comandos já mostrados)

```
sudo apt update
sudo apt install snmp-mibs-downloader
```
    
```
snmpbulkwalk -c public -v2c <hostname> | tee snmpbulk.txt
```

```
python snmp_process_list.py snmpbulk.txt
```

### Referência em vídeo

![type:video](https://youtube.com/embed/cPjZTqfkHbc)

[Identificação do serviço SNMP](https://www.youtube.com/watch?v=cPjZTqfkHbc&t=38s)  
[Enumeração SNMP utilizando nmap](https://www.youtube.com/watch?v=cPjZTqfkHbc&t=84s)  
[Descobrir o nome da community com onesixtyone e wordlists utililzadas](https://www.youtube.com/watch?v=cPjZTqfkHbc&t=139s)  
[Enumerando com snmpwalk](https://www.youtube.com/watch?v=cPjZTqfkHbc&t=277s)  
[Enumerando com snmp-check](https://www.youtube.com/watch?v=cPjZTqfkHbc&t=381s)  

## Enumeracao NFS

Enumeração com nmap do NFS

```
nmap -Pn -sV -p 111,2049 --script="banner,(rpcinfo or nfs*) and not (brute or broadcast or dos or external or fuzzer)" -oN "tcp_111_2049_nfs_nmap.txt" <hostname>
```

Mostra informações de compartilhamento da máquina alvo

```
showmount -e
```

Montando o compartilhamento na máquina

```
sudo mount -o rw,vers=2 <hostname>:/home /mnt
```

No entanto, no momento de montar o compartilhamento é necessário uma configuração de usuário para exploração desta falha. Vide [esta referência](../2_Exploracao/NFS.md)

## Enumeracao FTP

```
ftp -a <hostname>
```

ou para transferência de arquivos em modo ativo:

```
ftp -a -A <hostname>
```

**Se atentar ao fato de possíveis arquivos escondidos**

```
ls -al
```

## Active Directory

Primeiro precisamos de informações básicas a respeito do domínio ou quem é o DC (Domain Controller) para sabermos quem devemos ter como alvo para extração de hashes e afins...

### IP dos domain controllers

linux

```
nslookup -type=srv _ldap._tcp.dc._msdcs.<dominio.com.br>
nslookup -type=srv _kerberos._tcp.<dominio.com.br>
nslookup -type=srv _kpasswd._tcp.<dominio.com.br>
nslookup -type=srv _ldap._tcp.<dominio.com.br>
```
windows

```
nslookup
set type=all
_ldap._tcp.dc._msdcs.<dominio.com.br>
```
Outra opção seria:

```
nltest /dclist:<dominio.local>
```
 
**Vale considerar que este útlimo comando só funciona se a máquina já está no domínio.**

### Obter nome dominio

**SEM ACESSO INICIAL NA MÁQUINA**

No Linux

```
enum4linux -a <hostname_dc>
```

Utilizar também

```
netexec smb <hostname_dc>
```


**COM ACESSO NA MÁQUINA**

No Windows

```
[System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()
```

ou

*Vale considerar que este comando só funciona se a máquina já está no domínio*

```
whoami
```

### Enumerar usuarios

**SEM ACESSO INICIAL NA MÁQUINA**

```
kerbrute userenum -d dominio.local /usr/share/seclists/Usernames/xato-net-10-million-usernames.txt
```

ou

```
rpcclient -U <domain>/<usuario> <hostname_dc>
```

```
enumdomusers
srvinfo
querydominfo
enumdomgroups
querygroup 0x200
netshareenum
netshareenumall
```

ou 

```
enum4linux -a <hostname_dc>
```

Via SMB

```
netexec smb <hostname> --users
```

Ldapsearch

```
ldapsearch -x -H ldap://<hostname> -D '' -w '' -b "DC=<domain_name>,DC=<tld>"
```

**COM ACESSO NA MÁQUINA**

     net user
     net user /domain
     
     net user username /domain


### AsRep Roasting

```
impacket-GetNPUsers -usersfile usernames.txt domain.local/ --dc-host <hostname_dc>
```

Com base nessa enumeração, provável que já teremos alguma credencial válida no domínio, então seguimos para os [próximos passos aqui.](../2_Exploracao/Active%20Directory.md)

**Observação abaixo, caso tenha uma máquina fora do domínio**

    runas /netonly /user:domain.br\user powershell
(será solicitado credencial e pode ser informado uma credencial inválida que o comando funcionará, mas por motivos óbvios, quaisquer comando que utilizemos não se autenticará na rede)


## Outros

Verificar as credenciais (as default e as que conseguimos via enumeração) obtidas e realizar o bruteforce em TODOS os outros SERVIÇOS encontrados na máquina

Caso algum serviço identificado não mencionado neste resumo, verifique [esta referência](OSCP%20Enumeration.md)

## Referencias

[script nmap_enum](https://github.com/segurancadecomputadores/InfraHacking/blob/main/Tools/Scripts/nmap_enum.md)

Basta copiar para um arquivo em um diretório da sua máquina e relacioná-lo no /usr/bin com link simbólico, por exemplo:

```
sudo ln -s /home/user/Downloads/nmap_enum /usr/bin/nmap_enum
```

[urlExtract](https://github.com/eversinc33/urlExtract)

Para instalar, basta seguir as instruções do README.