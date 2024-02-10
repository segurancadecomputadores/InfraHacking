Flight
======

## Enumeration

Nmap script

![qownnotes-media-VMuwah](../../../media/qownnotes-media-VMuwah.png)

UDP scan

![qownnotes-media-VMgxgZ](../../../media/qownnotes-media-VMgxgZ.png)

Full TCP scan

![qownnotes-media-Wvevvm](../../../media/qownnotes-media-Wvevvm.png)

Então temos:

DNS
HTTP
Kerberos
RPC
SMB
LDAP


### HTTP

![qownnotes-media-zNgBXB](../../../media/qownnotes-media-zNgBXB.png)

![qownnotes-media-TwGaZc](../../../media/qownnotes-media-TwGaZc.png)

![qownnotes-media-VveKHl](../../../media/qownnotes-media-VveKHl.png)

![qownnotes-media-qQXkSz](../../../media/qownnotes-media-qQXkSz.png) =======> Possible entry point

Dando sequência após a enumeração dos demais. Nada encontrado exceto por este subdomínio:

    school.flight.htb

### SMB

![qownnotes-media-bkzCgk](../../../media/qownnotes-media-bkzCgk.png)

    g0.flight.htb

![qownnotes-media-DQoVIc](../../../media/qownnotes-media-DQoVIc.png)


### RPC

![qownnotes-media-kBtyxd](../../../media/qownnotes-media-kBtyxd.png)

### LDAP

![qownnotes-media-LnGbjN](../../../media/qownnotes-media-LnGbjN.png)

### Kerberos

    kerbrute userenum --domain flight.htb /usr/share/seclists/Usernames/xato-net-10-million-usernames.txt --dc 10.10.11.187

![qownnotes-media-RAnyke](../../../media/qownnotes-media-RAnyke.png)

### DNS

![qownnotes-media-OxlPjV](../../../media/qownnotes-media-OxlPjV.png)

![qownnotes-media-nyDtLD](../../../media/qownnotes-media-nyDtLD.png)

![qownnotes-media-OyCEfo](../../../media/qownnotes-media-OyCEfo.png)


### HTTP continue

    school.flight.htb

![qownnotes-media-iUmICa](../../../media/qownnotes-media-iUmICa.png)

![qownnotes-media-kMffXk](../../../media/qownnotes-media-kMffXk.png)

Aqui eu notei que o que muda é o parâmetro "view" da URL, então eu tentei esse mesmo teste, mas de maneira diferente:

    ffuf -c -ic -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http://school.flight.htb?view=FUZZ -e ".php,.html" -fs 1102

![qownnotes-media-XXURur](../../../media/qownnotes-media-XXURur.png)

![qownnotes-media-puTALa](../../../media/qownnotes-media-puTALa.png)

As que vieram diferentes foram:

![qownnotes-media-KrRjij](../../../media/qownnotes-media-KrRjij.png)


    g0.flight.htb
 
 ![qownnotes-media-CDlMtu](../../../media/qownnotes-media-CDlMtu.png)

Deu em nada aqui

No entanto, se formos parar para perceber, me parece que isso pode estar sendo consultado no banco de dados... Vamos para um outro teste :

#### SQLi

    ffuf -c -ic -w /usr/share/seclists/Fuzzing/SQLi/Generic-SQLi.txt -u http://school.flight.htb?view=FUZZ -e ".php,.html" -fs 7069,1170,1102


![qownnotes-media-NHAXrq](../../../media/qownnotes-media-NHAXrq.png)


![qownnotes-media-RlSMVb](../../../media/qownnotes-media-RlSMVb.png)

![qownnotes-media-SdlWrO](../../../media/qownnotes-media-SdlWrO.png)

![qownnotes-media-HtZCrq](../../../media/qownnotes-media-HtZCrq.png)

#### LFI

PWNED!

![qownnotes-media-GeVlbb](../../../media/qownnotes-media-GeVlbb.png)


## Exploitation

