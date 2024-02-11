Information Gathering Active Directory Domain (internal)
========================

Identificar as máquinas de domain controler em uma rede

## Fora do domínio

linux


    nslookup -type=srv _ldap._tcp.dc._msdcs.redecorp.br
    nslookup -type=srv _kerberos._tcp.EXMAPLE.COM
    nslookup -type=srv _kpasswd._tcp.EXAMPLE.COM
    nslookup -type=srv _ldap._tcp.EXAMPLE.COM
    nslookup -type=srv _ldap._tcp.dc._msdcs.EXAMPLE.COM
    
![qownnotes-media-OZugnN](../../media/qownnotes-media-OZugnN.png)

 
windows 
    nslookup
    set type=all
    _ldap._tcp.dc._msdcs.EXAMPLE.COM

### Enumeração de usuário
    
Para tentar obter alguma credencial válida no ambiente, é interessante utilizar a ferramenta kerbrute, que pode ser utilizada para "brutar" os domain controllers ou até mesmo fazer uma enumeração de usuário, por exempĺo:

    kerbrute userenum -d redecorp.br usernames_test.txt

source: https://github.com/ropnop/kerbrute

## Caso já temos credencial no domínio

A primeira coisa que é interessante considerarmos para enumeração de domínio na rede é estar com a máquina no domínio, claro. Para isso precisaremos de uma credencial ou ter uma máquina de domínio já comprometida. Caso já temos essa credencial, então devemos utilizá-la da seguinte forma em máquinas windows (que estejam fora do domínio):

    runas /netonly /user:domain.com\username powershell

Exemplo real:

    runas /netonly /user:redecorp.br\a0106003 powershell
    (será solicitado credencial e pode ser informado uma credencial inválida que o comando funcionará, mas por motivos óbvios, quaisquer comando que utilizemos não se autenticará na rede)
    
Dessa forma teremos acesso administrativo por meio de uma console de powershell e enviaremos as credenciais para quaisquer protocolos que utilizaremos


Outra opção seria:

        nltest /dclist:domainname

*Vale considerar que este comando só funciona se a máquina já está no domínio*

## Sem ferramentas externas

Utilizando o powershell para enumerar os domain controllers poderia ser da seguinte forma (OSCP method):

Para obter informações a respeito das máquinas e info de domínio:

    [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()


Para obter os endereços IPs dos domain controllers:

    [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain().DomainControllers.ipaddress

 
 Detalhes: System.DirectoryServices.ActiveDirectory é um namespace e Domain é a classe pertencente a esse namespace. GetCurrentDomain() é um método dentro desta classe Domain que chamamos para conseguir informações a respeito do domínio
 
 O próximo objetivo agora é utilizar de filtros pra realização de querys LDAP que podem ser encontradas por meio de duas classes diferentes, sendo elas System.DirectoryServices.DirectryEntry e System.DirectoryServices.DirectorySearcher e podemos montar esses objetos da seguinte maneira:
 
     $dentry = New-Object System.DirectoryServices.DirectoryEntry
     $searcher = New-Object System.DirectoryServices.DirectorySearcher([ADSI]$searchstring)
     $searcher.searchroot = $dentry #Essa chamada determina que ele deve ter como ponto de partida o $dentry
 
 Montando nossa query para identificar os grupos do domínio, devemos montar o seguinte filtro:
 
     $searcher.filter ="(objectClass=Group)"
     $result = $searcher.findall()
  
  Demonstrando o resultado de forma fácil de visualizar:
  
      foreach($obj in $result) {
          $obj.properties.name
      }
 
 Pra facilitar a visualização somente do que quer ser visto, é interessante filtrar o conteúdo, para tal ,podemos fazer o seguinte:
 
       #enumerar as propriedades do objeto
       $result | get-member

Com o resultado disto, sabemos por meio de qual campo devemos filtrar nosso output, sendo ele, nesse caso o "path":
 
     #o select-string funciona como se fosse o grep do linux
     $result.path | select-string "admin"
 
 
 Em resumo, o scipt todo ficaria desta forma:
 
### script completo para obtenção de informação
 
     $domain = [System.DirectoryServices.ActiveDirectory.Domain]::getcurrentdomain()

    #######
    ## LDA://BRNVNDCP001.REDECORP.BR/DC=REDECORP,DC=BR
    ## Esta parte consiste em montar a query string LDAP para obter as informações necessárias do servidor de Domain Controller que obtivemos por     meio do comando anterior
    #######
    $searchstring = "LDAP://" + $domain.pdcroleowner.name + "/"
    $distinguishedname = "DC=" + $domain.name.replace('.', ',DC=')
    $searchstring += $distinguishedname

    #########
    ## Neste ponto, estamos montando os filtros para obter as informações necessárias. Para tal, é necessário duas classes:
    ## DirectoryEntry e DirectorySearcher. Observem...
    ########
    $searcher = New-Object System.DirectoryServices.DirectorySearcher([ADSI]$searchstring)
    $d_entry = New-Object System.DirectoryServices.DirectoryEntry

    $searcher.searchroot = $d_entry

    ########
    ## A montagem dos filtros propriamente dito
    ##
    ########
    $searcher.filter = "(|(name=Domain Admins)(name=Administrators))" #Como exemplo, enumeração usuários dentro do grupo "Domain Admins" OU "Administrator"


    ######
    ## Obtençção do resultado
    ##
    ######
    $result = $searcher.findall()


    #result | get-member ### Neste ponto eu faço uma enumeração dos métodos e propriedades do objeto pra identificar o que exatamente e uquero obter de resultado

    foreach($obj in $result){
        $obj.properties.member
    }

## Enumerando domain controlers

Alterando somente a parte de filtro e de exebição do resultado do script anterior, teremos o seguinte resultado:cc
 
     $searcher.filter = "(&(objectCategory=Computer)(userAccountControl:1.2.840.113556.1.4.803:=8192))"
     $result = $searcher.findall()
 
     foreach($obj in $result){
        $obj.properties.dnshostname
     }
     
## Enumerando grupos de domínio
 
     $searcher.filter = "(objectClass=Group)"
     $result = $searcher.findall()
 
     foreach($obj in $result){
        $obj.properties.name
     }
     
## Enumerando usuários dentro do "Domain Admins"
     
     $searcher.filter = "(|(name=Domain Admins)(name=Administrators))" #Como exemplo, enumeração usuários dentro do grupo "Domain Admins" OU "Administrator"
     $result = $searcher.findall()
     foreach($obj in $result){
        $obj.properties.member
     }
     
## Com powerview

    get-netdomain
    
    get-domainuser "a0106003" #olhar um usuário específico