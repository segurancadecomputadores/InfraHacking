Mirai
========================

## Enumeration

    sudo nmap_enum mirai.htb | tee nmap_output.txt

![qownnotes-media-TlZjyp](../../../media/qownnotes-media-TlZjyp.png)

    sudo nmap -sU -p 53,161,111,137,139,500,2049 -Pn mirai.htb | tee nmap_udp_output.txt

![qownnotes-media-mZhyML](../../../media/qownnotes-media-mZhyML.png)

    sudo nmap -p- -Pn -T5 -n mirai.htb | tee nmap_fullportstcp_output.txt

![qownnotes-media-VgUQMp](../../../media/qownnotes-media-VgUQMp.png)

    sudo nmap -sS --script vuln -n -T5 -Pn mirai.htb -p 22,53,80,1774,32400,32469 | tee nmap_vulns.txt

![qownnotes-media-AfvVFc](../../../media/qownnotes-media-AfvVFc.png)

![qownnotes-media-yBkjlr](../../../media/qownnotes-media-yBkjlr.png)

![qownnotes-media-DQSaid](../../../media/qownnotes-media-DQSaid.png)

Analisando uma saída do nmap, dá pra notar um novo domínio:

    vi /etc/hosts
    (edição do arquivo e adicionei o pi.hole)

![qownnotes-media-Vjuvuk](../../../media/qownnotes-media-Vjuvuk.png)

    firefox http://pi.hole
 
 ![qownnotes-media-WMBses](../../../media/qownnotes-media-WMBses.png)

    searchsploit Pi-hole
    
   
![qownnotes-media-ULvluM](../../../media/qownnotes-media-ULvluM.png)

Com base nos exploits, procurei por uma credencial válida, sem sucesso. Tentativa com bruteforce:

    hydra pi.hole -l mirai.htb -P /usr/share/wordlists/rockyou.txt http-post-form "/admin/scripts/pi-hole/php/add.php:domain=mirai.htb&list=white&pw=^PASS^:Wrong password"

Tentei de uma outra forma também:

    hydra pi.hole -l teste -P /usr/share/wordlists/rockyou.txt http-post-form "/admin/index.php?login:pw=^PASS^:Wrong password"

Ainda estou procurando uma foma de conseguir credenciais:

    dnsrecon -d pi.hole -n 10.10.10.48
    
    dnsrecon -d mirai.htb -n 10.10.10.48
    
    gobuster dir -u http://pi.hole -w /usr/share/seclists/Discovery/Web-Content/common.txt -k -t 16 -o "tcp_port_protocol_s_ext_gobuster.txt"
    
    gobuster dir -u http://mirai.htb -w /usr/share/seclists/Discovery/Web-Content/common.txt -k -t 16 -o "tcp_port_protocol_s_ext_gobuster.txt"
    
![qownnotes-media-wkWJHa](../../../media/qownnotes-media-wkWJHa.png)


