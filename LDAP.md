LDAP
========================

Após a fase de enumeração, que pode ser encontrada [aqui](Infraestrutura%2FOSCP%20Enumeration.md#LDAP), podemos prosseguir com os demais comandos, tais como:

    ldapsearch -x -H ldap://<hostname> -b "DC=streamio,DC=htb" "*"

    ldapsearch -H ldap://10.10.11.158:3268/ -x -s base -b '' "(objectClass=*)" "*"
    
Com credencial

    ldapsearch -x -H ldap://10.129.210.134 -D "yoshihide@streamio.htb" -W -b "dc=streamio,dc=htb" "(objectclass=group)"
    
Command options explained:

-x use simple authentication (as opposed to SASL)
-H your AD server
-D the DN to bind to the directory. In other words, the user you are authenticating with.
-W Prompt for the password. The password should match what is in your directory for the the bindn (-D). Mutually exclusive from -w.
-b The starting point for the search

## EnumneracaoUsuarios