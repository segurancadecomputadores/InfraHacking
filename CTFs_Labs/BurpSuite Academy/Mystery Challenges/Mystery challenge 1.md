
## Enumeration


### Analisando o frontend
```
curl -H "Cookie: session=IXXW2yrGG0zEg4X6feIV6NtealMS5B5c" https://0a6c001b04bca922823e4ca300b10083.web-security-academy.net/ | ./extract.rb
```

![](../../../../media/Pasted%20image%2020240804143824.png)

Nada que ajude até então:


```
urlExtract https://0a6c001b04bca922823e4ca300b10083.web-security-academy.net/
```

![](../../../../media/Pasted%20image%2020240804143903.png)

Verifiquei o frontend da aplicação e nada demais pelo menos na primeira página:

![](../../../../media/Pasted%20image%2020240804143931.png)

Na página do produto também não achei nada por enquanto:


![](../../../../media/Pasted%20image%2020240804144031.png)

### Bruteforce de URL (diretórios e arquivos)


```
feroxbuster -u https://0a6c001b04bca922823e4ca300b10083.web-security-academy.net/ --extract-links -w /usr/share/seclists/Discovery/Web-Content/common.txt
```



![](../../../../media/Pasted%20image%2020240804144137.png)

```
feroxbuster -u https://0a6c001b04bca922823e4ca300b10083.web-security-academy.net/ --extract-links -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
```
![](../../../../media/Pasted%20image%2020240804144203.png)

Agora vamos tentar com extensões de arquivos:

```
feroxbuster -w /usr/share/seclists/Discovery/Web-Content/common.txt -u https://0a6c001b04bca922823e4ca300b10083.web-security-academy.net/ -x "txt,html,php,asp,aspx,jsp"
```

![](../../../../media/Pasted%20image%2020240804145550.png)


### Fuzzing de parâmetros

```
ffuf -ic -c -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt -u https://0a6c001b04bca922823e4ca300b10083.web-security-academy.net/product?FUZZ=
```


![](../../../../media/Pasted%20image%2020240804150037.png)

```
ffuf -ic -c -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt -u https://0a6c001b04bca922823e4ca300b10083.web-security-academy.net/product?productId=4\&FUZZ= -fs 4856
```


### scanning


```
nikto -host https://0a6c001b04bca922823e4ca300b10083.web-security-academy.net -T x 6 -useragent "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0"
```

![](../../../../media/Pasted%20image%2020240804151326.png)
### CVEs

N/A

### WAF

N/A

### Subdomínios

```
ffuf -c -ic -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -H "Host: FUZZ.0a6c001b04bca922823e4ca300b10083.web-security-academy.net" -u https://0a6c001b04bca922823e4ca300b10083.web-security-academy.net
```

![](../../../../media/Pasted%20image%2020240804151113.png)

### Enumerações específicas

N/A


## Exploitation

Parâmetros encontrados:

productId
message



1. [x] SQL Injection
2. [x] OS Command Injection
3. [ ] 

![](../../../../media/Pasted%20image%2020240804142339.png)