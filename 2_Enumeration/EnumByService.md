EnumByService
========================


	




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

### metasploit

no metasploit podemos utilizar o módulo 

    auxiliary/scanner/snmp/snmp_enum

, desta forma, podemos informar uma comunidade que já conhecemos ou tentar enumerar comunidades com o seguinte módulo:

    auxiliary/scanner/snmp/snmp_login



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

### Checking for null session or guest account

    cme smb 10.11.1.xxx -u '' -p ''
    cme smb 10.11.1.xxx -u 'guest' -p ''


    smbclient -L //10.10.10.103
    smbclient -L -U '' -N //10.10.10.103    

Para conseguir fazer enunmeração mais rapidamente, é interessante que utilizemos o recurso de recursividade no smbclient com os seguintes comandos:

    recurse on
    dir

### nbtscan

    sudo nbtscan -r 10.11.1.0/24

    
ou então um pouco mais de detalhes pode ser obtido como seguinte comando:

    enum4linux -a -A <IP_VICTIM>
    

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

 A premissa do NFS é o mesmo do SMB, compartilhar arquivos por meio da rede, acontece que no caso do NFS não é utilizado autenticação e autorização em suas versões 3 e 2, somente um grau de seguranã maior na sua versão 4.
 
 Para explorar essa vulnerabilidade, primeiro precisamos fazer a enumeração do serviço por meio dos seguintes comandos:
 
 
     sudo nmap -sS -p 111,2049 --script nfs-showmount,nfs-ls 10.11.1.72
     #outra possibilidade também pode ser:
     sudo apt update
     sudo apt install libnfs-utils
     #Com isso a gente consegue fazer a enumeração das permissividades dos arquivos em modo recursivo
     nfs-ls -R nfs://10.11.1.72/home
     
     showmount -e 10.11.1.72
     
     nmap -sV -p 111 --script=rpcinfo 10.11.1.1-254


![qownnotes-media-wrgegz](../../media/qownnotes-media-wrgegz.png)

### Exploitation

Assim que fizermos a enumeração, vamos identificar quais são os IDs pertinenetes aos arquivos lá constados no share via NFS, mas isso só é válido nas versões 2 e 3  do protocolo. A versão 4 já é mais segura! Basta adicionarmos o usuário e o grupo do usuário cujo uid e guid corespondem ao mesmo que foi identificao no momento da enumeração.

    sudo adduser --uid 1014 pwn
    sudo addgroup --gid 1014 pwn_group
    sudo usermod -a -G pwn_group pwn

    mount.nfs -o vers=3 10.11.1.72:/home /tmp/mount_dir
    
    

    

## DNS

### Enumerate domain

Primeiro temos que verificar se a gente consegue obter o nome do domínio por mieo de uma resolução invesrsa de DNS da seguinte maneira:
Informar o serviço de DNS encontrado por meio do comando abaixo:
    
    sudo nmap -sU -p 53 192.168.134.0/24 -oG dns_enum_lab.txt
    #Assim que achado o DNS server (porta 53 aberta), fazemos um nslookup ou host para um host da rede
    #host <IPS_DA_REDE> <IP_DNS_SERVER>
    host 192.168.134.149 192.168.134.149
    

    nslookup
    server 192.168.134.149    
![qownnotes-media-GwrWbS](../../media/qownnotes-media-GwrWbS.png)

    host -t ns mailman.com # Para encontrar o name server do domínio


### Enumerating subdomains


Brute-force: 

	dnsrecon -d target.com -D wordlist.txt -t brt
	
**OBS: wordlist can be found at:**

**C:\Users\olive\Desktop\acosta\owncloud\Tools_Utilities\hacking-tools\fuzzdb\discovery\dns**


### DNS cache snooping: 
	
	dnsrecon -t snoop -D wordlist.txt -n 2.2.2.2 where 2.2.2.2 is the IP of the target’s NS server
Options
--threads 8: Number of threads
-n nsserver.com: Use a custom name server
Output options
--db: SQLite 3 file
--xml: XML file
--json: JSON file
--csv: CSV file

### DNS Zone transfer

	dnsrecon -d target.com -t axfr
	
or
	
	host -l target.com <dns_server_address>
	
**OBS: First find which dns servers correspond to that domain with:**
	
	host -t ns target.com
	
![qownnotes-media-XJyjiZ](file://media/18886.png)

DNS Query types:

https://ns1.com/resources/dns-types-records-servers-and-queries

### Enumeração de DNS (domínio)

Preimeiro temos que identificar qual o servidor de DNS que responde por este serviço:

    nmap -p 53 -sU --open 10.11.1.0/24 -oG dns_servers.txt
    
Assim que obtido o servidor, ou seja, obter a resposta do comando acima para os IPs que responderam com "open", configuramos ele no arquivo /etc/resolv.conf ccom a seguinte diretiva:

    nameserver <IP_SERVER_DNS>

Feito isso, executamos o seguinte comando para obter o nome de domínio da rede alvo:

    fping -d -a -g 10.0.0.0/24 # -> reverse DNS lookup   
    #ou
    fping -d -A -a -g 10.0.0.0/24 #(para obter o endereço de IP ao lado do hostname)
    
![qownnotes-media-RkNxKi](../media/qownnotes-media-RkNxKi.png)


Depois, tentamos realizar uma transferência de zona para obter informações a respeito do domínio:

    host -t axfr thinc.local <IP_DNS_SERVER>
    #ou
    dig axfr thinc.local @<IP_DNS_SERVER> #Esse não funionou e não entendi o porquê
    #ou
    dnsenum thinc.local # pode ser informado a flag --dnsserver <IP_DNS_SERVER> para especificar o server de DNS
    
![qownnotes-media-TnBCdx](../media/qownnotes-media-TnBCdx.png)


Dessa forma obtemos todas as informações a respeito daquele domínio. ALém disto, observamos um subdomínio especial ali e tentamos uma transferência de zona lá também, sem sucesso, mas anda temos mais uma possibilidade que é enumerar os subdomínios da seguinte forma:

    dnsrecon -d _msdcs.thinc.local -D /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -t brt 

## HTTP
    
    sudo nmap -sS -p 80,443 --script "http* and not http-brute and not http-slowloris" -n 10.11.1.123

