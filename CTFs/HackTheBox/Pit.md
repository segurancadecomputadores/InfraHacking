Pit
========================

## Enumeration

Nmap Enumeration

![qownnotes-media-oSuKHg](../../../media/qownnotes-media-oSuKHg.png)



![qownnotes-media-ZUNyOn](../../../media/qownnotes-media-ZUNyOn.png)


![qownnotes-media-zGJMnO](../../../media/qownnotes-media-zGJMnO.png)

![qownnotes-media-dgEJDk](../../../media/qownnotes-media-dgEJDk.png)


### HTTP 9090 **(Possible entry point)**

Nmap version detection

![qownnotes-media-qZYYza](../../../media/qownnotes-media-qZYYza.png)

Web navigation

![qownnotes-media-uPAXok](../../../media/qownnotes-media-uPAXok.png)

Searchsploit **** (Esse cara aqui não tem nada a ver... O nome do sistema é **COCKPIT**)

![qownnotes-media-RcYEoF](../../../media/qownnotes-media-RcYEoF.png)

![qownnotes-media-QDjYXC](../../../media/qownnotes-media-QDjYXC.png)

Nikto

![qownnotes-media-dHBlNn](../../../media/qownnotes-media-dHBlNn.png)

gobuster

![qownnotes-media-iyzlFv](../../../media/qownnotes-media-iyzlFv.png)


### HTTTP 80

Subdomain Enumeration

    ffuf -c -ic -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -H "Host: FUZZ.pit.htb" -u http://pit.htb -fs 4057

![qownnotes-media-yjbVXF](../../../media/qownnotes-media-yjbVXF.png)

![qownnotes-media-SwjMfG](../../../media/qownnotes-media-SwjMfG.png)

### SSLSCAN

Com isso deu pra descobrir o domínio:

dms-pit.htb

![qownnotes-media-PdTJbC](../../../media/qownnotes-media-PdTJbC.png)

#### gobuster

![qownnotes-media-mHYQjU](../../../media/qownnotes-media-mHYQjU.png)

Subdomain enumeration

    ffuf -c -ic -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -H "Host: FUZZ.dms-pit.htb" -u http://dms-pit.htb

![qownnotes-media-HeiXAn](../../../media/qownnotes-media-HeiXAn.png)

### SNMP 161

    onesixtyone pit.htb -c /usr/share/seclists/Discovery/SNMP/common-snmp-community-strings-onesixtyone.txt

    snmp-check -w -c public 10.10.10.241

![qownnotes-media-uzEqCg](../../../media/qownnotes-media-uzEqCg.png)

![qownnotes-media-flsgis](../../../media/qownnotes-media-flsgis.png)

Usando agora o nmap

    sudo nmap -sU -p 161 --script "snmp*" pit.htb

Depois de enumerar tudo, fiquei travado e fui consultar um especialista e cheguei a seguinte conclusão:


Aqui na enumeração SNMP temos algo que devemos levar em conta:

Eu fiz uma alteração no arquivo de configuração /etc/snmp/snmp.conf apenas comentando uma linha no arquivo...
```
└─$ cat /etc/snmp/snmp.conf 
# As the snmp packages come without MIB files due to license reasons, loading
# of MIBs is disabled by default. If you added the MIBs you can reenable
# loading them by commenting out the following line.
#mibs : =====> ESTA LINHA FOI COMENTADA

# If you want to globally change where snmp libraries, commands and daemons
# look for MIBS, change the line below. Note you can set this for individual
# tools with the -M option or MIBDIRS environment variable.
#
# mibdirs /usr/share/snmp/mibs:/usr/share/snmp/mibs/iana:/usr/share/snmp/mibs/ietf
```

Então eu tentei com dois comandos diferentes:

    snmpwalk -c public -v1 10.10.10.241 | tee snmpwalk_v1.txt
     wc -l snmpwalk_v1.txt       
       1567 snmpwalk_v1.txt

![qownnotes-media-glnpJL](../../../media/qownnotes-media-glnpJL.png)

Depois de ter comentado a linha no arquivo de configuração, note que apareceram algumas coisas a mais no output do comando

    snmpwalk -v1 -c public 10.10.10.241 . | tee snmpwalk_full.txt

O memso ocorre para este comando:

![qownnotes-media-jhVPzV](../../../media/qownnotes-media-jhVPzV.png)

Porém, neste último comando, vêm mais informações do que no comando anterior. Vide que foi possível enumerar inclusive os usuários do servidor:

![qownnotes-media-oDTggp](../../../media/qownnotes-media-oDTggp.png)

![qownnotes-media-RwctWY](../../../media/qownnotes-media-RwctWY.png)

    wc -l snmpwalk_full.txt 
    1685 snmpwalk_full.txt

E uma aplicação web que está no diretório /var/www:

![qownnotes-media-LtUYpC](../../../media/qownnotes-media-LtUYpC.png)
