# OWASP Web Methodology

## Passive info gathering (WSTG-INFO-01)

<https://leak-lookup.com/>

  
### whois enumeration
  Trivial as:

	whois <domain>
	
###  Search engine
  As explained in the next sections
  
  source:
https://gbhackers.com/latest-google-dorks-list/#:~:text=Google%20Dorks%20List%20%E2%80%9CGoogle%20Hacking,interesting%20information%20from%20unsecured%20Websites.

https://github.com/BullsEye0/google_dork_list/blob/master/google_Dorks.txt

```
site:amil.com.br -www
inurl:
intitle:
intext:
inbody:
filetype:pdf
site:domain.com.br .git
site:github.com domain gwt (google web toolkit)  #source code disclosure
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
```
###  netcraft
	https://searchdns.netcraft.com
![qownnotes-media-qUqXKM](file://media/25457.png)


## Fingerprint WebServer (WSTG-INFO-02)

    httprint -h https://domain.com.br -s /usr/share/httprint/signatures.txt -P0
    
    sudo nmap -sS -p 443,80 --script http-enum domain.com.br
    
    whatweb https://$url -a 4 --user-agent "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0"