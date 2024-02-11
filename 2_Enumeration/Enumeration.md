Enumeration
========================

# Traceroute
Com o traceroute, podemos enumerar a infraestrutura de uma empresa contando o número de saltos que um pacote realiza para chegada até a Internet ou para alguma outra rede dentro da própria corporação.
O conceito técnico do traceroute é dado da seguinte maneira (comecemos pelo básico):

Estrutura do protocolo IP:

![qownnotes-media-IbFIir](file://media/2378.png)

Version:4 or 6, em bits ficaria 
Campo de 1 byte ou 4 bits que determina se é versão 4 ou 6 do protocolo.

Os valores correspondentes em bits são 0100 e 0110

![qownnotes-media-VFSbQA](file://media/15791.png)


# Hosts ativos na rede
Podemos realizar este tipo de varredura com o fping

    fping -a -q -g 10.0.0.0/24  -> pingar uma vez cada host da rede especificada com a opção -g
    
    fping -q -g 10.0.0.0/24 -a -> só mostra os hosts ativos --alive e -q de quiet
    
    fping -d -g 10.0.0.0/24 -> reverse DNS lookup 
    
    fping -i 1 -g 10.0.0.1/24 -> fastest ping scan

## ping scan with bettercap 

bettercap -eval "net.probe on; sleep 3;q"

formatting the response

	bettercap -eval "net.probe on; sleep 3;q" | cut -d' ' -f 4 | sed ':a;N;$! ba;s/\n/,/g'


## NMAP

    nmap -sn -n 10.11.1.0/24

# Enumeração de DNS (domínio)

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
    

![qownnotes-media-nsaqgy](../media/qownnotes-media-nsaqgy.png)




# scanning for ports and vulnerabilities

## nmap

	nmap --script vuln hosts
	
	nmap --script all -Pn 192.168.1.30
	
	sudo nmap -sS --script vulners -sV 10.11.1.209
	
	sudo nmap -sS --script vulners -sV --version-intensity 9 10.11.1.209
	
##  Scanning for UDP ports

    nmap -sU -p 53,161,111,137,139,2049 -Pn 10.11.1.0/24

### html output
    nmap -O -A -sU -sV -Pn --webxml -oX off_sec_lab_nmap.xml (For UDP scan)
    xsltproc off_sec_lab_nmap.xml -o off_sec_lab_nmap.html

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

# SNMP

O serviço de SNMP é interessante para fins de enumeração. Para tanto, podemos utilizar basicamente 4 ferramentas:

## onesixtyone

Primeiro vamos utilizar de alguns arquivos para armazenar as informações necessárias:

    echo public > community
    echo manager >> community
    echo private >> community

    sudo nmap -p 161 -sU -n 

    onesixtyone -c community -i snmp_ips.txt

## snmpwalk

com o SNMPWALK podemos tentar enumerar a topologia de rede da corporação com as seguintes ferramentas:

    snmpwalk -c public -v 2c <VICTIM_IP>
    
#### Windows users
    
    snmpwalk -c public -v1 10.11.1.14 1.3.6.1.4.1.77.1.2.25
    
#### Windows processes

    snmpwalk -c public -v1 10.11.1.73 1.3.6.1.2.1.25.4.2.1.2

#### Windows TCP open ports
    
    snmpwalk -c public -v1 10.11.1.14 1.3.6.1.2.1.6.13.1.3    

#### Windows installed software:

    snmpwalk -c public -v1 10.11.1.50 1.3.6.1.2.1.25.6.3.1.2

por exemplo, onde -c é o parâmetro para informar a comunidade (public/PUBLIC por padrão)

## metasploit

no metasploit podemos utilizar o módulo 

auxiliary/scanner/snmp/snmp_enum

, desta forma, podemos informar uma comunidade que já conhecemos ou tentar enumerar comunidades com o seguinte módulo:

auxiliary/scanner/snmp/snmp_login

## snmpcheck

 Não funcionou quando eu tentei... Depois eu verifico o problema.


# SMB

Podemos considerar algumas ferramentas para fazer a checagem desse serviço. São elas:

## nmap (scripts)

    ls -1 /usr/share/nmap/scripts/smb*

    nmap -v -p 139,445 -oG smb.txt 10.11.1.1-254
    
    nmap -v -p 139, 445 --script=smb-os-discovery 10.11.1.227
    
    nmap -v -p 139,445 --script smb-vuln* 192.168.0.0/24
    
    sudo nmap -sS --script "smb* and not smb-flood and not smb-brute" 10.11.1.123

## nbtscan

    sudo nbtscan -r 10.11.1.0/24

## enum4linux

    enum4linux <IP_VICTIM>
    
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
    
# NFS

     sudo nmap -sS -p 111,2049 --script nfs-showmount,nfs-ls 10.11.1.72
     
## nfs-ls (libnfs-utils)
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

# Sniffing de rede
Mesmo sem man-in-the-middle, é possível observar se os roteadores estão com HSRP habilitados em sua interface, dado que eles mandam esses  pacotes em multicast.
Isto possibilita MAN-IN-THE-Middle attacks. É possível também observar para onde o tráfego de rede está indo. Enfim... uma série de coisas que ainda vou relatar.


GET /dsadsa HTTP/1.0