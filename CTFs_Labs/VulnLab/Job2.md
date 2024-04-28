Job2
========================
IP: 10.10.106.153

## Enumeracao

Comecando com enumeracao basica:

![qownnotes-media-RsxeDq](../../../../media/qownnotes-media-RsxeDq.png)

![qownnotes-media-aeKYsW](../../../../media/qownnotes-media-aeKYsW.png)

Agora enumerando as possiveis vulnerabilidades tambem

    sudo nmap -sS --script vuln -Pn -T5 -p 22,25,80,111,135,139,443,445,1063,2049,3389,10001,10002,10003 job2.vl


## Enumeracao HTTP 

    feroxbuster -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-big.txt -u http://job2.vl

### scan de vulnerabilidades

    nikto -host http://job2.vl -T x 6

![qownnotes-media-xQctkb](../../../../media/qownnotes-media-xQctkb.png)

### Enumeracao de subdominio

    ffuf -c -ic -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -H "Host: FUZZ.job2.vl" -H "User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0" -u http://job2.vl

![qownnotes-media-pXopkx](../../../../media/qownnotes-media-pXopkx.png)

    cat "www.job2.vl >> /etc/hosts"

    ffuf -c -ic -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -H "Host: FUZZ.www.job2.vl" -H "User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0" -u https://www.job2.vl



### Enumeracao de diretorios e arquivos

    feroxbuster -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-big.txt -u http://www.job2.vl

    ffuf -ic -c -w /usr/share/seclists/Discovery/Web-Content/common.txt -e ".txt,.php,.asp,.aspx,.html" -u http://www.job2.vl/FUZZ

Nada de interessante

### Outras enumeracoes

    sslscan https://www.job2.vl

![qownnotes-media-pfoWUr](../../../../media/qownnotes-media-pfoWUr.png)

    sudo nmap -sS --version-intensity 9 -sV -p 1063,10001,10002,10003 10.10.106.153

## Enumeracao SMB e RPC

    smbclient -U '' -N -L //10.10.106.153
    
    smbclient -L //10.10.106.153

![qownnotes-media-mSjCIM](../../../../media/qownnotes-media-mSjCIM.png)

    enum4linux -a -A 10.10.106.153
    
    crackmapexec smb 10.10.106.153 -u 'guest' -p '' --shares

Nada de interessante
    
    rpcclient //10.10.106.153
    rpcclient -U '' -N 10.10.106.153

## SMTP Enumeration

    smtp-user-enum -M RCPT -D job2.vl -U usernames.txt -t 10.10.106.153

![qownnotes-media-GAFsUO](../../../../media/qownnotes-media-GAFsUO.png)


![qownnotes-media-EENedY](../../../../media/qownnotes-media-EENedY.png)

![qownnotes-media-gCNPtm](../../../../media/qownnotes-media-gCNPtm.png)

![qownnotes-media-RBEILW](../../../../media/qownnotes-media-RBEILW.png)

    telnet 10.10.106.153 25
    ehlo teste
    vrfy admin
    vrfy administrator
(desabilitado)

![qownnotes-media-BWWGYW](../../../../media/qownnotes-media-BWWGYW.png)

## Bruteforce SSH

    hydra -l hr -P /usr/share/wordlists/rockyou.txt ssh://10.10.106.153

## Enumeracao RDP

    sudo nmap -sS -p 3389 --version-intensity 9 -sV --script "rdp*" 10.10.106.153

![qownnotes-media-jdJDxI](../../../../media/qownnotes-media-jdJDxI.png)

## Enumeracao NFS e RPC

    nmap -Pn -sV -p 111,2049 --script="banner,(rpcinfo or nfs*) and not (brute or broadcast or dos or external or fuzzer)" -oN "tcp_111_2049_nfs_nmap.txt" 10.10.106.153

![qownnotes-media-KJAOkk](../../../../media/qownnotes-media-KJAOkk.png)

