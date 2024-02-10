21 - Active Directory Attacks
========================

# Enumerate

## Privileged users
  
    $ldapFilter = "(&(objectclass=user)(memberof=CN=Domain Admins,CN=Users,DC=xor,DC=com))"
    $domain = New-Object System.DirectoryServices.DirectoryEntry
    $search = New-Object System.DirectoryServices.DirectorySearcher
    $search.SearchRoot = $domain
    $search.PageSize = 1000
    $search.Filter = $ldapFilter
    $search.SearchScope = "Subtree"
    #Execute Search
    $results = $search.FindAll()
    Foreach($obj in $results) {
      Foreach($prop in $obj.Properties){ 
          $prop
        }
    }
 
Onde DC (no filtro, primeira linha do script) ali vai ser o nome do domínio que estamos analisando.


## Service Principal Names

    setspn -F -Q */*

Another method

    $ldapFilter = "(&(objectclass=user)(objectcategory=user)(servicePrincipalName=*))"
    $domain = New-Object System.DirectoryServices.DirectoryEntry
    $search = New-Object System.DirectoryServices.DirectorySearcher
    $search.SearchRoot = $domain
    $search.PageSize = 1000
    $search.Filter = $ldapFilter
    $search.SearchScope = "Subtree"
    #Execute Search
    $results = $search.FindAll()
    #Display SPN values from the returned objects
    foreach ($result in $results)
    {
        $userEntry = $result.GetDirectoryEntry()
        Write-Host "User Name = " $userEntry.name
        foreach ($SPN in $userEntry.servicePrincipalName)
            {
                Write-Host "SPN = " $SPN      
            }
        Write-Host ""   
    }

    impacket-GetUsersSPNs
    
**DEPOIS DE OBTER ESCALAÇÃO DE PRIVILÉGIO**

 1- Verificar quem está logado na máquina
 
     