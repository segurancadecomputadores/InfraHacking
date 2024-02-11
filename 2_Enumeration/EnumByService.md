EnumByService
========================



Verbose

    	nmap -v --script all hosts

Para mais detalhes em outros tipos de scan com nmap pode verificar [esta nota](..%2FTools%2FNmap.md)

## netcat

    nc -z -w 1 -n -v 10.11.1.70 500
    nc -z -w 1 -n -v 10.11.1.70 500-1000

    nc -u -z -w 1 -n -v 10.11.1.70 161

## bash pseudo devices


    /dev/$protocol/$host/$port

    if timeout 5 bash -c '</dev/tcp/kernel.org/443 &>/dev/null'
    then
      echo "Port is open"
    else
      echo "Port is closed"
    fi

ou

    timeout .1 bash -c "echo /dev/tcp/10.11.1.5/80" && echo "port 80 is open"


## Legion

    sudo legion
	

![qownnotes-media-ZtomcZ](file://media/1239443923.png)


## metasploit

no metasploit podemos utilizar o módulo 

auxiliary/scanner/snmp/snmp_enum

, desta forma, podemos informar uma comunidade que já conhecemos ou tentar enumerar comunidades com o seguinte módulo:

auxiliary/scanner/snmp/snmp_login


## SNMP

snmp enumeration

```
    sudo nmap -sU --open -p 161 10.11.1.1-254 -oG open-snmp.txt

There is a module on metasploit:
auxiliary/scanner/snmp/snmp_enum
set RHOSTS `ip`

default configuration
Module options (auxiliary/scanner/snmp/snmp_enum):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   COMMUNITY  public           yes       SNMP Community String
   RETRIES    1                yes       SNMP Retries
   RHOSTS     10.12.24.1       yes       The target address range or CIDR identifier
   RPORT      161              yes       The target port (UDP)
   THREADS    1                yes       The number of concurrent threads
   TIMEOUT    1                yes       SNMP Timeout
   VERSION    1                yes       SNMP Version <1/2c>

```
![qownnotes-media-gSQJLi](../../media/qownnotes-media-gSQJLi.png)

Considerar

### SNMPWALK

```
snmpwalk -c public -v1 -t 10 10.11.1.14
```

**Windows users**

```
snmpwalk -c public -v1 10.11.1.14 1.3.6.1.4.1.77.1.2.25
```

**Windows processes**

```
snmpwalk -c public -v1 10.11.1.73 1.3.6.1.2.1.25.4.2.1.2
```

**Windows TCP open ports**

```
snmpwalk -c public -v1 10.11.1.14 1.3.6.1.2.1.6.13.1.3    
```

**Windows installed software:**

```
snmpwalk -c public -v1 10.11.1.50 1.3.6.1.2.1.25.6.3.1.2
```

### onesixtyone

```
echo public > community
echo private >> community
echo manager >> community
for ip in $(seq 1 254); do echo 10.11.1.$ip; done > ips
onesixtyone -c community -i ips


onesixtyone 192.168.4.0/24 public

onesixtyone -c dict.txt -i hosts -o my.log -w 100
```

### SNMPCHECK

```
snmp-check -w -c public <hostname>
```

### snmpbulkwalk

```
sudo apt update
sudo apt install snmp-mibs-downloader

snmpbulkwalk -c public -v2c <hostname> | tee snmpbulk.txt
python snmp_process_list.py snmpbulk.txt
```

#### snmp\_process\_list.py

```
#!/usr/bin/env python3

import re
import sys
from collections import defaultdict
from dataclasses import dataclass


@dataclass
class Process:
    """Process read from SNMP"""
    pid: int
    proc: str
    args: str = ""

    def __str__(self) -> str:
        return f'{self.pid:04d} {self.proc} {self.args}'


with open(sys.argv[1]) as f:
    data = f.read()

processes = {}

for match in re.findall(r'HOST-RESOURCES-MIB::hrSWRunName\.(\d+) = STRING: "(.+)"', data):
    processes[match[0]] = Process(int(match[0]), match[1])

for match in re.findall(r'HOST-RESOURCES-MIB::hrSWRunParameters\.(\d+) = STRING: "(.+)"', data):
    processes[match[0]].args = match[1]

for p in processes.values():
    print(p)
```


## snmpcheck

 Não funcionou quando eu tentei... Depois eu verifico o problema.


## SMB

Podemos considerar algumas ferramentas para fazer a checagem desse serviço. São elas:

### nmap (scripts)

    ls -1 /usr/share/nmap/scripts/smb*

    nmap -v -p 139,445 -oG smb.txt 10.11.1.1-254
    
    nmap -v -p 139, 445 --script=smb-os-discovery 10.11.1.227
    
    nmap -v -p 139,445 --script smb-vuln* 192.168.0.0/24
    
    sudo nmap -sS --script "smb* and not smb-flood and not smb-brute" 10.11.1.123

    nmap -v -p 139,445 --script smb-vuln* 192.168.0.0/24
    
### MS-08-067

	nmap --script smb-vuln-ms08-067.nse -p 445 10.102.80.0/24
    
   
	
Esse comando pode derrubar o servidor, então temos que ficar atentos com isso em redes produtivas.

    nmap --script smb-vuln-ms08-067.nse --script-args=unsafe=1 -p 445 10.102.80.0/24
  

### ms-17-010

    nmap -p 445,139 -iL file_with_targets.txt --script smb-vuln-ms17-010


### scanning for vulnerabilities

	nmap --script=vuln host
	
	nmap --script vulscan/vulscan.nse --script-args vulscanoutput=listid
	
	
	nmap --script vulners -sV -sS 10.11.1.250
OBS: É importante ter o -sV para fazer a identificação pela versão do serviço.
	
### enum4linux

    enum4linux <IP_VICTIM>


### RCE vulnerabilities

    smb-vuln-ms08-067.nse,smb-vuln-ms17-010

### just SMB

smb-os-discovery, smb-enum-processes,smb-system-info.nse


## nbtscan

    sudo nbtscan -r 10.11.1.0/24

    
ou então um pouco mais de detalhes pode ser obtido como seguinte comando:

    enum4linux -a -A <IP_VICTIM>
    
## Checking for null session or guest account

    cme smb 10.11.1.xxx -u '' -p ''
    cme smb 10.11.1.xxx -u 'guest' -p ''


    smbclient -L //10.10.10.103
    smbclient -L -U '' -N //10.10.10.103    

Para conseguir fazer enunmeração mais rapidamente, é interessante que utilizemos o recurso de recursividade no smbclient com os seguintes comandos:

    recurse on
    dir

Desta forma, basta exxecutar esses dois comandos para ele fazer a busca via dir recursivamente
    
## NFS

     sudo nmap -sS -p 111,2049 --script nfs-showmount,nfs-ls 10.11.1.72
     
     
### nfs-ls (libnfs-utils)
     #outra possibilidade também pode ser:
     sudo apt update
     sudo apt install libnfs-utils
     #Com isso a gente consegue fazer a enumeração das permissividades dos arquivos em modo recursivo
     nfs-ls -R nfs://10.11.1.72/home
     
     showmount -e 10.11.1.72
     
     nmap -sV -p 111 --script=rpcinfo 10.11.1.1-254


# SMTP

    nmap -sS -p 25 -n -sV 10.11.1.25
    
    nc -nv 10.11.1.217 25
    VRFY root
    VRFY idontexist
    
    VRFY <username>
    
    user <username>
    pass <password>
    list
    
    retr 1
    retr 2
    
# RDP

        sudo nmap -sS --script vulners,rdp* 10.11.1.123
    
# HTTP
    
    sudo nmap -sS -p 80,443 --script "http* and not http-brute and not http-slowloris" -n 10.11.1.123

