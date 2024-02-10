Registry
========================

## Enumeration

Basic enumeration
    sudo nmap_enum 10.129.239.237

Full TCP enumeration
 
    sudo nmap -sS -p- -T5 -n 10.129.239.237
    
![qownnotes-media-Pcvlha](../../../media/qownnotes-media-Pcvlha.png)

 
 UDP Enumeration
 
     sudo nmap -sU -p 53,161,111,137,139,2049 -Pn 10.129.239.237
     
![qownnotes-media-UxPBlj](../../../media/qownnotes-media-UxPBlj.png)

HTTP Enumeration

    gobuster dir -u http://10.129.239.237 -w /usr/share/seclists/Discovery/Web-Content/common.txt -t 16
 
 
![qownnotes-media-sGibvU](../../../media/qownnotes-media-sGibvU.png)


    gobuster dir -u https://10.129.239.237/install -w /usr/share/seclists/Discovery/Web-Content/common.txt -t 16 -k
    
![qownnotes-media-IcAuqW](../../../media/qownnotes-media-IcAuqW.png)


## scanning

    nikto -h http://10.129.239.237 -Tuning x 6

![qownnotes-media-YJVgzQ](../../../media/qownnotes-media-YJVgzQ.png)

O primeiro passo aqui já foi interessante, porque achamso um subdomínio por meio do certificado  da aplicação;

    sslscan https://10.129.239.237
    
![qownnotes-media-goDPDk](../../../media/qownnotes-media-goDPDk.png)


    cat /etc/hosts
    
![qownnotes-media-WEXlQO](../../../media/qownnotes-media-WEXlQO.png)



    nikto -h http://docker.registry.htb -Tuning x 6

![qownnotes-media-ljveZN](../../../media/qownnotes-media-ljveZN.png)

    
https://docker.registry.htb/v2/

Notamos que nessa URL é solicitado credencial... Começamos pelo básico:


admin

admin

