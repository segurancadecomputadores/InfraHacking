---
Title: Hacking>SNMP
Contents: SNMP
tag: Hacking>SNMP
...
---
SNMP
========================
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

![qownnotes-media-ZsRaRI](../media/qownnotes-media-ZsRaRI.png)


Considerar 

## SNMPWALK

    snmpwalk -c public -v1 -t 10 10.11.1.14
    
#### Windows users
    
    snmpwalk -c public -v1 10.11.1.14 1.3.6.1.4.1.77.1.2.25
    
#### Windows processes

    snmpwalk -c public -v1 10.11.1.73 1.3.6.1.2.1.25.4.2.1.2

#### Windows TCP open ports
    
    snmpwalk -c public -v1 10.11.1.14 1.3.6.1.2.1.6.13.1.3    

#### Windows installed software:

    snmpwalk -c public -v1 10.11.1.50 1.3.6.1.2.1.25.6.3.1.2

## onesixtyone

    echo public > community
    echo private >> community
    echo manager >> community
    for ip in $(seq 1 254); do echo 10.11.1.$ip; done > ips
    onesixtyone -c community -i ips


    onesixtyone 192.168.4.0/24 public
    
    onesixtyone -c dict.txt -i hosts -o my.log -w 100
    
## SNMPCHECK

    snmp-check -w -c public <hostname>

## snmpbulkwalk

    sudo apt update
    sudo apt install snmp-mibs-downloader
    
    snmpbulkwalk -c public -v2c <hostname> | tee snmpbulk.txt
    python snmp_process_list.py snmpbulk.txt
    
### snmp_process_list.py

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