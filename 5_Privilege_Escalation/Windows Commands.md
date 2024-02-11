Windows Commands
========================



# stop defenses

## Disable ASLR in windows

A registry setting is available to forcibly enable or disable ASLR for all executables and libraries and is found at HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management\MoveImages

## stopping symantec endpoint protection

taskkill /f /im ccsvchst.exe


# Recon

## Versao do windows
Trás a versão do sistema operacional

	Winver 

Trás a versão do hardware

Wmic computersystem get model
   
	Systeminfo


## View policies applied

    gpresult /scope User /v
    gpresult /scope Computer /v

# Persistence

## Add admin account in windows
source:
https://www.richardawilson.com/2010/06/add-user-as-local-administrator-on.html

net user /add user_name password 

net localgroup Administrators /add user_name

## Enable SMB/CIFS 1.0

optionalfeatures


## add route

route add 10.10.1.0 mask 255.255.255.0 192.168.1.100
          > net    >net mask    > gateway
  
 É necessário estar como administrador para inserir rotas no windows.
 
 
 Running process in background
 
 start (running programs in back ground)
 
## Desativar firewall do windows
netsh firewall set opmode disable (OLD)

netsh advfirewall set allprofiles state off

Restart da interface de rede do windows
netsh winsock reset

adicionar usuário na grupo de administradores do windows
net localgroup group_name UserLoginName /add


## Starting word in "Run"
winword


## networking
netsh wlan show profiles

netsh wlan show profiles name="SSID_rede" key=clear

Utilitários
========================

cmd commands

## Enable RDP


    net start termservice
    
    netsh firewall add portopening tcp 3389 "RDP"
    
## searching files

    cd \
    dir proof.txt /s /p


## verify domain users in windows

net user user_name /domain

## update de GPOs

    gpupdate

## getting Windows defender status

get-mpcomputaerstatus

## windows firewall in command line
source:
https://felipegbass.wordpress.com/2012/07/07/gerenciando-o-firewall-do-windows-via-command-prompt/

### bypassing policy firewall rules:

If rules are not applied in the local computer, verify if another vendor is using firewall configurations of the machine.
source: https://support.symantec.com/us/en/article.tech197660.html


source:https://www.stigviewer.com/stig/windows_7/2013-10-01/finding/V-17422 

Check Text ( C-45364r1_chk )
If the following registry value does not exist or is not configured as specified, this is a finding:

Registry Hive: HKEY_LOCAL_MACHINE
Subkey: \Software\Policies\Microsoft\WindowsFirewall\DomainProfile\

Value Name: AllowLocalPolicyMerge

Type: REG_DWORD
Value: 1

OR

### Configuring windows firewall in command line
Configure the policy value for Computer Configuration -> Windows Settings -> Security Settings -> Windows Firewall with Advanced Security -> Windows Firewall with Advanced Security -> Windows Firewall Properties (this link will be in the right pane) -> Domain Profile Tab -> Settings (select Customize) -> Rule merging, "Apply local firewall rules" to "No".

desativar a firewall no perfil Domínio
	
	netsh advfirewall set domainprofile state off
 
desativar a firewall no perfil Privado
	
	netsh advfirewall set privateprofile state off
 
desativar a firewall no perfil Público
	
	netsh advfirewall set publicprofile state off
 
desativar a firewall para todos os perfis
	
	netsh advfirewall set all state off / Netsh advfirewall set allprofiles state off
 
Ativara firewall no perfil Domínio
	
	netsh advfirewall set domainprofile state on
 
Ativara firewall no perfil Privado
	
	netsh advfirewall set privateprofile state on
 
Ativara firewall no perfil Público
	
	netsh advfirewall set publicprofile state on
 
Ativara firewall para todos os perfis
	
	netsh advfirewall set all state on
 
Exportar todas as configurações da firewall
	
	netsh advfirewall export c:\advfirewall.wfw
 
Importar ficheiro com as configurações da firewall
	
	netsh advfirewall import c:\advfirewall.wfw
 
Adicionar uma excepção para uma aplicação em específico
	
	netsh advfirewall firewall add rule name=regra_para_permitir_acesso_inbound_do_notepad dir=in program=c:\Windows\System32\notepad.exe  action=allow
 
Criar uma excepção para o porto 80 do protocolo TCP
	
	netsh advfirewall firewall add rule name= permitir_trafego_porto_80 dir=in action=allow protocol=TCP localport=80
 
Repor as configurações originais da firewall
	
	netsh advfirewall reset
 
Registar todas as ligações ignoradas pela firewal referente a todos os perfis
	
	netsh advfirewall set allprofiles logging droppedconnections enable
 
Registar todas as ligações efectuadas com sucesso referente a todos os perfis
	
	netsh advfirewall set allprofiles logging allowedconnections enable
 
Verificar as configurações gerais da firewall referente a todos os perfis
	
	netsh advfirewall show allprofiles

Since October 2016, Microsoft has been using a new servicing model for the supported versions of Windows Server updates. This new servicing model for distributing updates simplifies the way that security and reliability issues are addressed. Microsoft recommends keeping your systems up-to-date to make sure that they are protected and have the latest fixes applied.

This threat can run the following commands:

	netsh ipsec static add policy name=netbc
	netsh ipsec static add filterlist name=block
	netsh ipsec static add filteraction name=block action=block
	netsh ipsec static add filter filterlist=block any srcmask=32 srcport=0 dstaddr=me dstport=445 protocol=tcp description=445
	netsh ipsec static add rule name=block policy=netbc filterlist=block filteraction=block
	netsh ipsec static set policy name=netbc assign=y

It can also add firewall rules to allow connections by using these commands:

	netsh advfirewall firewall add rule name="Chrome" dir=in program="C:\Program Files (x86)\Google\Chrome\Application\chrome.txt" action=allow
	netsh advfirewall firewall add rule name="Windriver" dir=in program="C:\Program Files (x86)\Hardware Driver Management\windriver.exe" action=allow

Source: https://support.microsoft.com/en-sg/help/4471134/smb-sharing-not-accessible-when-tcp-port-445-listening-in-windows-serv
##########################################
##    Criando uma imagem de backup do windows    ##
##########################################
1 - Control Panel
2 - System and security
3 - Backup and restore (windows7)
4 - Create a system image
5 - Choose the local to save the backup ( i used the backup hard drive)
and wait...

Fonte:  https://www.windowscentral.com/how-make-full-backup-windows-10

############################################
##  Restore point in windows 10 (ponto de restauração) ##
############################################
1 - Open start
2 - create a restore point

## Stopping AVs
source:
https://blog.netspi.com/10-evil-user-tricks-for-bypassing-anti-virus/
Stop and Disable Anti-Virus Services
In some cases users don’t have the privileges to disable anti-virus via the GUI, but they do have control over the associated services. If that is the case, then anti-virus services can usually be stopped and disabled. This can be accomplished via services.msc, the “sc” command, or the “net stop” command. However, always make sure to be a good little pentester and restore the services to their original state before logging out of the system. To stop a Windows service issue the following command:

net stop “service name”
To disable a Windows service issue the following command:

sc config "service name" start= disabled
The services.msc console can be also be used to stop and disabled services via a GUI interface.  It can be accessed by navigating to start->run, and typing “services.msc”.

## Viewing windows logs

<QueryList>
  <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='TargetUserName']='sadailton']] and TimeCreated[@SystemTime&gt;='2019-09-05T15:23:58.000Z' and @SystemTime&lt;='2019-09-05T22:23:58.999Z']]]</Select>
  </Query>
</QueryList>

## Preventing changing proxy in windows

Start  > Run > “Gpedit.msc”.

omputer Configuration > Administrative Templates >Windows Components > Internet Explorer  > and Enable the “Prevent changing Proxy Settings policy“