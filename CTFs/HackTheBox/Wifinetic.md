Wifinetic
========================

    sudo nmap_enum 10.10.11.247 | tee nmap_output.txt

![qownnotes-media-lCcdPM](../../../media/qownnotes-media-lCcdPM.png)

    sudo nmap -sU -p 53,161,111,137,139,500,2049 -Pn 10.10.11.247 | tee nmap_udp_output.txt

![qownnotes-media-nFyzXP](../../../media/qownnotes-media-nFyzXP.png)

    sudo nmap --script vuln -p 21,22,53 -n -Pn 10.10.11.247 | tee nmap_vulns.txt