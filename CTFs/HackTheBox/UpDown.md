UpDown
========================

## Enumeration

    sudo nmap_enum updown.htb | tee nmap_output.txt

![qownnotes-media-uCjFKZ](../../../media/qownnotes-media-uCjFKZ.png)

    sudo nmap -sU -p 53,161,111,137,139,500,2049 -Pn updown.htb | tee nmap_udp_output.txt
    
![qownnotes-media-hRoFZT](../../../media/qownnotes-media-hRoFZT.png)

    sudo nmap -p- -Pn -T5 -n updown.htb | tee nmap_fullportstcp_output.txt

![qownnotes-media-naKMic](../../../media/qownnotes-media-naKMic.png)

    sudo nmap -sS --script vuln -n -T5 -Pn updown.htb -p 80,22 | tee nmap_vulns.txt

![qownnotes-media-MnBVdY](../../../media/qownnotes-media-MnBVdY.png)


    nikto -host http://updown.htb -T x 6 | tee nikto_output.txt

![qownnotes-media-JVtppj](../../../media/qownnotes-media-JVtppj.png)

Subdomain

    ffuf -c -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -H "Host: FUZZ.updown.htb" -u http://updown.htb -fw 186
    
![qownnotes-media-lWHsiW](../../../media/qownnotes-media-lWHsiW.png)


    gobuster dir -u http://updown.htb -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -x "txt,html,php,asp,aspx,jsp" -k -t 16 -o "tcp_port_protocol_gobuster.txt"
    
    
    
    gobuster dir -u http://updown.htb/dev -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -x "txt,html,php,asp,aspx,jsp" -k -t 16 -o "tcp_port_protocol_gobuster.txt"
    
    gobuster dir -u http://updown.htb -w /usr/share/seclists/Discovery/Web-Content/common.txt -x "txt,html,php,asp,aspx,jsp" -k -t 16 -o "tcp_port_protocol_ext_gobuster.txt"
    
![qownnotes-media-cFwoiz](../../../media/qownnotes-media-cFwoiz.png)

![qownnotes-media-psBLqC](../../../media/qownnotes-media-psBLqC.png)

![qownnotes-media-zuvPUS](../../../media/qownnotes-media-zuvPUS.png)

    echo 10.10.11.177    siteisup.htb >> /etc/hosts

    gobuster dir -u http://siteisup.htb/dev -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -x "txt,html,php,asp,aspx,jsp" -k -t 16 -o "tcp_port_protocol_gobuster.txt"

![qownnotes-media-hKFjEq](../../../media/qownnotes-media-hKFjEq.png)

Subdomain
    
    ffuf -c -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -H "Host: FUZZ.siteisup.htb" -u http://siteisup.htb -fw 186

![qownnotes-media-bEmcUY](../../../media/qownnotes-media-bEmcUY.png)


    ffuf -u http://dev.siteisup.htb/FUZZ -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -e ".txt,.html,.php,.asp,.aspx,.jsp" -fc 403
 
 ![qownnotes-media-zUYeLJ](../../../media/qownnotes-media-zUYeLJ.png)
