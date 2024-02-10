Cereal
========================

## Enumeration

    sudo nmap_enum cereal.htb

![qownnotes-media-fSKApN](../../../media/qownnotes-media-fSKApN.png)

    sudo nmap -sU -p 53,161,111,137,139,500,2049 -Pn cereal.htb | tee nmap_udp_output.txt

![qownnotes-media-PAvBaP](../../../media/qownnotes-media-PAvBaP.png)

    sudo nmap -p- -Pn -T5 -n <hostname> | tee nmap_fullportstcp_output.txt

![qownnotes-media-VYVXAc](../../../media/qownnotes-media-VYVXAc.png)

    sudo nmap -sS --script vuln -n -T5 -Pn cereal.htb -p 22,80,443 | tee nmap_vulns.txt    
    
![qownnotes-media-RdoOAJ](../../../media/qownnotes-media-RdoOAJ.png)

### Scan

    nikto -host https://cereal.htb -T x 6 | tee nikto_output.txt

![qownnotes-media-njYAFA](../../../media/qownnotes-media-njYAFA.png)

    nikto -host http://cereal.htb -T x 6 | tee nikto_output.txt

**AQUI UM PONTO INTERESSANTE**

    sslscan cereal.htb

![qownnotes-media-GJYgbb](../../../media/qownnotes-media-GJYgbb.png)


![qownnotes-media-EKKpLe](../../../media/qownnotes-media-EKKpLe.png)

    gobuster dir -u http://source.cereal.htb -w /usr/share/seclists/Discovery/Web-Content/common.txt -k -t 16 -o "tcp_port_protocol_s_ext_gobuster.txt"

![qownnotes-media-cOhLez](../../../media/qownnotes-media-cOhLez.png)


    echo "10.10.10.217    source.cereal.htb >> /etc/hosts"

    wget https://raw.githubusercontent.com/internetwache/GitTools/master/Dumper/gitdumper.sh
    chmod +x gitdumper.sh
    ./gitdumper.sh http://source.cereal.htb/.git/ source_cereal/

 ![qownnotes-media-TvFrkh](../../../media/qownnotes-media-TvFrkh.png)

    ls -al
    git reset --hard
    ls

![qownnotes-media-kRGEgw](../../../media/qownnotes-media-kRGEgw.png)

