recon-ng tutorial
========================

source: https://www.blackhillsinfosec.com/whats-changed-in-recon-ng-5x/

## installing recon-ng

	test@ubuntu:~/$ git clone https://github.com/lanmaster53/recon-ng.git
	test@ubuntu:~/$ cd recon-ng
	test@ubuntu:~/recon-ng/$ pip install -r REQUIREMENTS

	./recon-ng -w UHG

This starts a new workplace called 'UHG'
	
	
## initiating recon-ng

	marketplace install all
	keys list
	
github API key

    https://github.com/settings/tokens/new
    
Add key to recon-ng
 
     keys add github_api < insert shodan api key here >        
     
 To see missing dependencies
 
     marketplace info metacrawler
     
 
	
## insert data into database

	db insert companies
or 
	
	db insert companies Company name~Company description~Some notes
	
	db insert domain cognitoforms.com~Some notes if required


## Using recon-ng

search for a module for github:

    modules search
    
   than i find the module 'recon/profiles-repositories/github_repos'
   
    [recon-ng][default] > modules load recon/profiles-repositories/github_repos  
    [recon-ng][default][github_repos] > help  
      
    Commands (type [help|?] <topic>):  
    ---------------------------------  
    back            Exits the current context  
    dashboard       Displays a summary of activity  
    db              Interfaces with the workspace's database  
    exit            Exits the framework  
    goptions        Manages the global context options  
    help            Displays this menu  
    info            Shows details about the loaded module  
    input           Shows inputs based on the source option  
    keys            Manages third party resource credentials  
    modules         Interfaces with installed modules  
    options         Manages the current context options  
    pdb             Starts a Python Debugger session (dev only)  
    reload          Reloads the loaded module  
    run             Runs the loaded module  
    script          Records and executes command scripts  
    shell           Executes shell commands  
    show            Shows various framework items  
    spool           Spools output to a file  
      
      
 than i was trying to find a way to make this work:
 
     info
 
set some infos:

    db insert domains www.amil.com.br~Site da amil
    
    [recon-ng][UHG] > show domains  
      
      +-------------------------------------------------------+  
      | rowid |      domain     |    notes     |    module    |  
      +-------------------------------------------------------+  
      | 1     | www.amil.com.br | Site da Amil | user_defined |  
      +-------------------------------------------------------+  
      
    [*] 1 rows returned  
    [recon-ng][UHG] > db insert companies  
    company (TEXT): UHG  
    description (TEXT): UHG Brasil  
    notes (TEXT): Esta eh a compania UHG  
    [*] 1 rows affected.  
    [recon-ng][UHG] > show companies  
      
      +-----------------------------------------------------------------------+  
      | rowid | company | description |         notes          |    module    |  
      +-----------------------------------------------------------------------+  
      | 1     | UHG     | UHG Brasil  | Esta eh a compania UHG | user_defined |  
      +-----------------------------------------------------------------------+  
      
    [*] 1 rows returned  
    [recon-ng][UHG] > db insert companies Amil~Referente a companhia comprada pela UHG~Nada  
    [*] 1 rows affected.  
    [recon-ng][UHG] > show companies  
      
      +---------------------------------------------------------------------------------------------------+  
      | rowid | company |               description               |         notes          |    module    |  
      +---------------------------------------------------------------------------------------------------+  
      | 1     | UHG     | UHG Brasil                              | Esta eh a compania UHG | user_defined |  
      | 2     | Amil    | Referente a companhia comprada pela UHG | Nada                   | user_defined |  
      +---------------------------------------------------------------------------------------------------+  
      
    [*] 2 rows returned  
 
 
 Defining options for modules:
 
     modules load recon/hosts-hosts/resolve
     options set SOURCE /home/kali/hostnames.txt
     run
 
 Depois de ter executado esses comandos, se obteve um resultado, mas que não consegui concluir se de fato entrou no banco de dados:
 
     modules load recon/netblocks-companies/whois_orgs
     info
 
 Aqui eu concluí após executar o comando que se trata somente de IPs, então fui para o próximo:
 
     modules load recon/domains-domains/brute_suffix
     info
     run
     
 Este módulo informou várias coisas ao banco de dados de maneira desnecessária. Para arrumar foi preciso os seguintes comandos:
 
     db delete hosts
         133-156
         
Depois de deletada todas as linhas desnecessárias, continuamos com outro módulos pra verificar quais mais fazem sentido...

    modules load recon/domains-hosts/google_site_web
    info
    options set SOURCE gvt.com.br
    run
    
 **Interessanteeste módulo**
 A tabela hosts foi atualizada, por um outro lado, não foi possível achar todos os hosts encontrados polo Sublist3r e sendo assim tive de importar alguns hosts a partir de um TXT:
 
     modules load import/list
     info
     options set COLUMN host
    options set TABLE hosts
    options set FILENAME /home/kali/hostnames.txt
    run
    
  Beleza... Agora sim, partiu resolver o nome de todo mundo:
  
    modules load recon/hosts-hosts/resolve
    info
    run
    
Beleza... todos os hosts resolvidos, dentro da possibilidade...

    modules load modules load recon/hosts-hosts/ssltools
    run

**Interessanteeste módulo**

Este módulo é interessante, porque ele junta o nome que já está na tabela domains e mete bronca pra identificar demais hosts como se fossem subdomínios:

    modules load recon/domains-hosts/brute_hosts
    info
    run
    
    modules load recon/hosts-hosts/reverse_resolve
    info
    run
    
 Foram encontrados outros domínios também pertencentes a GVT, portanto vamos refazer o brute force:
 
     
