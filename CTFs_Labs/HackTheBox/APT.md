APT
========================

UDP scan

![qownnotes-media-QkbmMT](../../../media/qownnotes-media-QkbmMT.png)

nmap script

![qownnotes-media-ABXAQS](../../../media/qownnotes-media-ABXAQS.png)

TCP Full scan

![qownnotes-media-AFEWWp](../../../media/qownnotes-media-AFEWWp.png)


Então temos somente Web e RPC

RPC

    rpcclient -U '' -N 10.10.10.213
    
    rpcclient -U 'guest' -N 10.10.10.213

Sem chance

Web (HTTP/80)
O scan nos retrata que não tem nada demais até esse momento:

    nikto -host http://apt.htb -T x 6 | tee nikto_output.txt
    
    
![qownnotes-media-tKfOwW](../../../media/qownnotes-media-tKfOwW.png)

Nada de diretório novo encontrado dentro do servidor:

    gobuster dir -u http://apt.htb -w /usr/share/seclists/Discovery/Web-Content/common.txt -k -t 16 -o "tcp_port_protocol_s_ext_gobuster.txt"

![qownnotes-media-wZzFEW](../../../media/qownnotes-media-wZzFEW.png)


Subdomínio não encontrado. Isso siginifica que as opções estão diminuindo...

    ffuf -c -ic -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -H "Host: FUZZ.apt.htb" -u http://apt.htb -fs 14879

![qownnotes-media-ZgunSi](../../../media/qownnotes-media-ZgunSi.png)

Aprofundando os testes de diretório:

    gobuster dir -u http://apt.htb -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -x "txt,html,php,asp,aspx,jsp" -k -t 16 -o "tcp_port_protocol_gobuster.txt"

