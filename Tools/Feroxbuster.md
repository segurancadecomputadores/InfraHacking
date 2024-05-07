FeroxBuster
========================
Comando mais utilizados:

## Comandos mais utilizados

### CTFs/Provas

    feroxbuster -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-big.txt -u http://<hostname> -t 16 --extract-links

Autenticado (Tem que testar)

    feroxbuster -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-big.txt -u http://<hostname> -t 16 --extract-links -b "Cookies: xxx"

Debug

    feroxbuster -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-big.txt -u http://<hostname> -t 16 --extract-links -b "Cookies: xxx" --proxy http://127.0.0.1:8080

### Produção

**Detalhe importantíssimo que essa ferramenta não possui suporte a delay dentre as requisições feitas. Mais fácil detecção. Uma forma da gente ser menos invasivo seria por meio de rate-limit. Vide link abaixo:**

<https://epi052.github.io/feroxbuster-docs/docs/examples/rate-limit/>

    feroxbuster -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-big.txt -u http://<hostname> -H "User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0" -t 16 --extract-links --rate-limit 1

O -A também seria uma opção para randomizar os agentes utilizados pela ferramenta.
O -o ou --output para salvar em um arquivo


Com output




## Referências de comandos


**Basic Usage:**

```
feroxbuster -u <url>
```

**Com wordlist:**

```
feroxbuster -u <url> -w <wordlist>
```

**Rodar com threads:**

```
feroxbuster -u <url> -t <number_of_threads>
```

**User-Agent:**

```
feroxbuster -u <url> -H "User-Agent: <user_agent>"
```

**Uso de cookies:**

```
feroxbuster -u <url> -C <cookie>
```

**Segue os redirecionamentos:**

```
feroxbuster -u <url> -r
```

**Filtro de status code:**

```
feroxbuster -u <url> --filter-status <status_code>
```

**Verbose:**

```
feroxbuster -u <url> -v
```

**Verificação SSL:**

```
feroxbuster -u <url> --insecure
```

**Configurar timeout:**

```
feroxbuster -u <url> --timeout <timeout_in_seconds>
```