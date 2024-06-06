Ffuf
========================

## Comandos mais usados - Enumeração

### CTFs/Provas:

Enumerando alvos

    

Enumerando arquivos e diretórios

    ffuf -c -ic -u http://<hostname>/FUZZ -ic -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -e ".txt,.html,.php,.asp,.aspx,.jsp" -fs <filtros> -H "User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0"

Subdomínios (virtual hosts)

    ffuf -c -ic -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -H "Host: FUZZ.<hostname>" -H "User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0" -u http://<hostname> -fs xxx

### Produção

Adicionado delay dentre as requisições para evitar bloqueios/detecções

    ffuf -c -ic -u http://<hostname>/FUZZ -ic -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -e ".txt,.html,.php,.asp,.aspx,.jsp" -fs <filtros> -H "User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0" -p "0.1-2.0"

ou com saída para salvar de evidência:

    ffuf -c -ic -u http://<hostname>/FUZZ -ic -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -e ".txt,.html,.php,.asp,.aspx,.jsp" -fs <filtros> -H "User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0" -p "0.1-2.0" -o output.txt

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

