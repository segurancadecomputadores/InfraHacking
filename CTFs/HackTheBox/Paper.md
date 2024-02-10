Paper
========================

## Enumeration

    sudo nmap_enum paper.htb | tee nmap_output.txt

![qownnotes-media-IPYFGI](../../../media/qownnotes-media-IPYFGI.png)

    
    sudo nmap -sU -p 53,161,111,137,139,500,2049 -Pn paper.htb | tee nmap_udp_output.txt
    
![qownnotes-media-zDMcVP](../../../media/qownnotes-media-zDMcVP.png)


    sudo nmap -p- -Pn -T5 -n paper.htb | tee nmap_fullportstcp_output.txt

![qownnotes-media-RSlCRr](../../../media/qownnotes-media-RSlCRr.png)


    sudo nmap -sS --script vuln -n -T5 -Pn paper.htb -p 22,80,443 | tee nmap_vulns.txt

![qownnotes-media-pJRInc](../../../media/qownnotes-media-pJRInc.png)


    gobuster dir -u http://paper.htb -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -x "txt,html,php,asp" -k -t 16 -o "tcp_port_protocol_gobuster.txt"
    
    gobuster dir -u https://paper.htb -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -x "txt,html,php" -k -t 16 -o "tcp_port_protocol_https_gobuster.txt"
    
    nikto -host http://paper.htb -T x 6 | tee nikto_output.txt
 
![qownnotes-media-WWaGlv](../../../media/qownnotes-media-WWaGlv.png)

    nikto -host https://paper.htb -T x 6 | tee nikto_https_output.txt

![qownnotes-media-FZpRQS](../../../media/qownnotes-media-FZpRQS.png)

    gobuster dir -u https://paper.htb -w /usr/share/seclists/Discovery/Web-Content/common.txt -k -t 16 -o "tcp_port_protocol_ext_gobuster.txt"

![qownnotes-media-MFQxjj](../../../media/qownnotes-media-MFQxjj.png)

    
    gobuster dir -u http://paper.htb -w /usr/share/seclists/Discovery/Web-Content/common.txt -k -t 16 -o "tcp_port_protocol_ext_gobuster.txt"
    
![qownnotes-media-xRFWIi](../../../media/qownnotes-media-xRFWIi.png)

    ffuf -c -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -H "Host: FUZZ.paper.htb" -u http://paper.htb -fs 199691
    
![qownnotes-media-JElydk](../../../media/qownnotes-media-JElydk.png)

    ffuf -c -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -H "Host: paper.FUZZ" -u http://paper.htb -fs 199691

![qownnotes-media-IETDVJ](../../../media/qownnotes-media-IETDVJ.png)
