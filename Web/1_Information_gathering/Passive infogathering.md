Passive infogathering
========================

## Passive information gathering

 Na prática, podemos utilizar as seguintes ferramentas para responder as seguintes perguntas:

 - What the applications do?
 - What language is it written in?
 -  what server software is tha application running on?
  
### 2 - whois enumeration
  Trivial as:

	whois <domain>
	
### 3 - Google Dork
  As explained in the next sections
  
  source:
https://gbhackers.com/latest-google-dorks-list/#:~:text=Google%20Dorks%20List%20%E2%80%9CGoogle%20Hacking,interesting%20information%20from%20unsecured%20Websites.

https://github.com/BullsEye0/google_dork_list/blob/master/google_Dorks.txt

site:amil.com.br -www

filetype:pdf

site:cnn.com

-site:www.cnn.com

"enable secret" ext:cfg -git -cisco.com
!Host=*.* intext:enc_UserPassword=* ext:pcf
!Host=. intext:enc_UserPassword= ext:pcf
" -FrontPage-" ext:pwd inurl:(service | authors | administrators | users)
" -FrontPage-" ext:pwd inurl:(service | authors | administrators | users) " -FrontPage-" inurl:service.pwd
" Dumping data for table (username|user|users|password)"
" Dumping data for table"
" phpMyAdmin MySQL-Dump" "INSERT INTO" -"the"

### 4 - netcraft
	https://searchdns.netcraft.com
![qownnotes-media-qUqXKM](file://media/25457.png)

### 5 - searching for subdomains

**sublist3r**

python sublist3r -d \<domain\>

![qownnotes-media-eYJOpJ](media/29314.png)

### 7 - recon-ng

tutorial:
https://hackertarget.com/recon-ng-tutorial/

first commands:
	marketplace install hackertarget
	modules load hackertarget
	show options
	info
	options set SOURCE cognitoforms.com
	run
	show hosts
  
### 8 - DNS
 
#### nslookup


	nslookup www.solarbr.com.br

#### dnsrecon

	root@kali:~# dnsrecon -r 200.217.149.195/32  
	[*] Reverse Look-up of a Range  
	[*] Performing Reverse Lookup from 200.217.149.195 to 200.217.149.195  
	[+] 0 Records Found  
	root@kali:~# dnsrecon -r 200.249.34.3/32  
	[*] Reverse Look-up of a Range  
	[*] Performing Reverse Lookup from 200.249.34.3 to 200.249.34.3  
	[+] 0 Records Found  
	root@kali:~# nslookup www.solarbr.com.br  
	Server:         192.168.1.1                   
	Address:        192.168.1.1#53  
	  
	Non-authoritative answer:  
	www.solarbr.com.br      canonical name = web1303.kinghost.net.  
	Name:   web1303.kinghost.net  
	Address: 191.6.194.81  
	Name:   web1303.kinghost.net  
	Address: 2804:10:4058::194:81  
	  
	root@kali:~# dnsrecon -r 192.6.194.81/32  
	[*] Reverse Look-up of a Range  
	[*] Performing Reverse Lookup from 192.6.194.81 to 192.6.194.81  
	[+] 0 Records Found  
	root@kali:~#  


### crt.sh

Open in browser or curl for automation purposes
https://crt.sh/?q=\<domain\>

## the Harvester


theHarvester -d example.domain.com -l 500 -b all

