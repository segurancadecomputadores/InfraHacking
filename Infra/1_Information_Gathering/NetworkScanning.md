NetworkScanning
========================


## Netdiscover

    netdiscover -i eth0 -P -N | cut -d ' ' -f 2

## Bettercap

    bettercap -eval "net.probe on; sleep 3;q" | cut -d' ' -f 4 | grep "\([0-9]\{1,3\}\)\.\([0-9]\{1,3\}\)\.\([0-9]\{1,3\}\).\([0-9]\{1,3\}\)" | sed $'s/\[[0-9]{1}m//g' > ips.txt

    bettercap -eval "net.probe on; sleep 3;q"

formatting the response

	bettercap -eval "net.probe on; sleep 3;q" | cut -d' ' -f 4 | sed ':a;N;$! ba;s/\n/,/g'

## Fping
Podemos realizar este tipo de varredura com o fping

    fping -a -q -g 10.0.0.0/24  -> pingar uma vez cada host da rede especificada com a opção -g
    
    fping -q -g 10.0.0.0/24 -a -> só mostra os hosts ativos --alive e -q de quiet
    
    fping -d -g 10.0.0.0/24 -> reverse DNS lookup 
    
    fping -i 1 -g 10.0.0.1/24 -> fastest ping scan

## NMAP

    nmap -sn -n 10.11.1.0/24
    
## Python
```
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
```