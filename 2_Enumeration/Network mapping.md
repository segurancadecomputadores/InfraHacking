Network mapping
============

# Nmap

Comandos interessantes para obter inforamações a respeito dos sistemas:
    
    nmap -p 445,139 --script smb-system-info.nse 192.168.1.109 --vulns

# Traceroute

Com o traceroute, podemos enumerar a infraestrutura de uma empresa contando o número de saltos que um pacote realiza para chegada até a Internet ou para alguma outra rede dentro da própria corporação.
O conceito técnico do traceroute é dado da seguinte maneira (comecemos pelo básico):

Estrutura do protocolo IP:

![qownnotes-media-IbFIir](file://media/2378.png)

Version:4 or 6, em bits ficaria 
Campo de 1 byte ou 4 bits que determina se é versão 4 ou 6 do protocolo.

Os valores correspondentes em bits são 0100 e 0110

![qownnotes-media-VFSbQA](file://media/15791.png)

# Sniffing de rede
Mesmo sem man-in-the-middle, é possível observar se os roteadores estão com HSRP habilitados em sua interface, dado que eles mandam esses  pacotes em multicast.
Isto possibilita MAN-IN-THE-Middle attacks. É possível também observar para onde o tráfego de rede está indo. Enfim... uma série de coisas que ainda vou relatar.

# scanning for vulnerabilities

	nmap --script vuln hosts
	
	nmap --script all -Pn 192.168.1.30

## getting verbose output

	nmap -v --script all hosts
	
	
# Hosts ativos na rede
Podemos realizar este tipo de varredura com o fping

fping -g 10.0.0.0/24 -c 1  -> pingar uma vez cada host da rede especificada com a opção -g

fping -q -g 10.0.0.0/24 -c 1 -a -> só mostra os hosts ativos --alive e -q de quiet

fping -n -g 10.0.0.0/24 -> reverse DNS lookup 

fping -i 1 -g 10.0.0.1/24 -> fastest ping scan

![qownnotes-media-ZtomcZ](file://media/1239443923.png)

# SNMPWALK
com o SNMPWALK podemos tentar enumerar a topologia de rede da corporação com as seguintes ferramentas:

snmpwalk -c public -v 2c 

por exemplo, onde -c é o parâmetro para informar a comunidade (public/PUBLIC por padrão)

# DNS Enumeration

dnsenum domain.com

dnsenum grurpoamil.com.br

ou

no metasploit podemos utilizar o módulo 

auxiliary/scanner/snmp/snmp_enum

, desta forma, podemos informar uma comunidade que já conhecemos ou tentar enumerar comunidades com o seguinte módulo:

auxiliary/scanner/snmp/snmp_login

## ping scan with bettercap 

bettercap -eval "net.probe on; sleep 3;q"

formatting the response

	bettercap -eval "net.probe on; sleep 3;q" | cut -d' ' -f 4 | sed ':a;N;$! ba;s/\n/,/g'
	
	
## ping sweep with python

import os
import platform

from datetime import datetime
net = input("Enter the Network Address: ")
net1= net.split('.')
a = '.'

net2 = net1[0] + a + net1[1] + a + net1[2] + a
st1 = int(input("Enter the Starting Number: "))
en1 = int(input("Enter the Last Number: "))
en1 = en1 + 1
oper = platform.system()

if (oper == "Windows"):
   ping1 = "ping -W 2 -n 1 "
elif (oper == "Linux"):
   ping1 = "ping -W 2 -c 1 "
else :
   ping1 = "ping -W 2 -c 1 "
t1 = datetime.now()
print ("Scanning in Progress:")

for ip in range(st1,en1):
   addr = net2 + str(ip)
   comm = ping1 + addr
   response = os.popen(comm)

   for line in response.readlines():
      if (line.upper().count("TTL")):
         print (addr, "--> Live")

t2 = datetime.now()
total = t2 - t1
print ("Scanning completed in: ",total)


    
    