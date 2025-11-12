Ffuf
========================

## Comandos mais usados - Enumeração

### CTFs/Provas:

Enumerando alvos

    

Enumerando arquivos e diretórios

```
ffuf -c -ic -u http://<hostname>/FUZZ -ic -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -e ".txt,.html,.php,.asp,.aspx,.jsp" -fs <filtros> -H "User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0"
```

Subdomínios (virtual hosts)

```
ffuf -c -ic -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -H "Host: FUZZ.<hostname>" -H "User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0" -u http://<hostname> -fs xxx
```

### Produção

Adicionado delay dentre as requisições para evitar bloqueios/detecções

```
ffuf -c -ic -u http://<hostname>/FUZZ -ic -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -e ".txt,.html,.php,.asp,.aspx,.jsp" -fs <filtros> -H "User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0" -p "0.1-2.0"
```

ou com saída para salvar de evidência:

```
ffuf -c -ic -u http://<hostname>/FUZZ -ic -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -e ".txt,.html,.php,.asp,.aspx,.jsp" -fs <filtros> -H "User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0" -p "0.1-2.0" -o output.txt
```

Parâmetros explicados:

-c cor na saída do comando
-ic ignorar comentários das wordlists
-u url
-e extensões que são concatenadas ao fim de cada palavra da wordlist
-f* filtros
-H Qualquer cabeçalho que quiser adicionar, caso informe um já existente nas requisições padrão, a ferramenta os substitui na requisição
-o para escrever em um arquivo


## Comandos Utilizados mais utilizados exploração

Com o comando abaixo, podemos informar numeros de 1-100

```
seq 1 100 | ffuf -u http://control.htb/view_product.php?id=FUZZ -w -
```

Utilizando 2 dicionarios

```
ffuf -u 'http://ffuf.me/cd/param/DIR?PARAM=1' -w /usr/share/seclists/Discovery/Web-Content/common.txt:DIR -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt:PARAM
```

Concatenando dois payloads:

```
seq 1 1000 | ffuf -u http://control.htb/view_product.php?id=IDFUZZ -w -:ID -w /usr/share/seclists/Fuzzing/SQLi/quick-SQLi.txt -fs 1339
```

Fazendo a concatencao de 1 em 1 para evitar overload:

```
seq 1 1000 | ffuf -u http://control.htb/view_product.php?FUZZ=ID -w -:ID -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt -fs 1339 -x http://127.0.0.1:8080 -mode pitchfork
```

### post requests


```
ffuf -w /usr/share/SecLists/Usernames/top-usernames-shortlist.txt -X POST -d "username=FUZZ&&password=x" -H "Content-Type: application/x-www-form-urlencoded" -u http://mydomain.com/login -mr "username already exists"
```

salvar a request que interceptou do BURP, onde for injetar o payload basta informar FUZZ no formato que já estamos acostumados e passar como parâmetro para o ffuf (igual do sqlmap):

```
seq 1 254 | ffuf -request req.txt -w -
```

```
cat req.txt
```
```
POST /product/stock HTTP/2
Host: 0af6003b03aa122781ec202b007500bb.web-security-academy.net
Cookie: session=1onNY6Jfm3MfVU8UrJJiDTtHiLqHRiad
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: */*
Accept-Language: pt-BR,pt;q=0.8,en-US;q=0.5,en;q=0.3
Accept-Encoding: gzip, deflate, br
Referer: https://0af6003b03aa122781ec202b007500bb.web-security-academy.net/product?productId=1
Content-Type: application/x-www-form-urlencoded
Content-Length: 96
Origin: https://0af6003b03aa122781ec202b007500bb.web-security-academy.net
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: same-origin
Priority: u=0
Te: trailers

stockApi=http%3A%2F%2F192.168.0.FUZZ%3A8080%2Fadmin
```

## Aguardando um retorno (string) específico

```
ffuf -u http://teste.com -mr "teste" -w wordlist.txt
```