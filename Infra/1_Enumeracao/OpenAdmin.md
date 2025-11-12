
## Enumeration

```
sudo nmap -p- -Pn -T5 -n 10.10.10.171 | tee nmap_fullportstcp_output.txt
```

![](../../../media/Pasted%20image%2020240908141549.png)


```
feroxbuster -u http://10.10.10.171/ -w /usr/share/seclists/Discovery/Web-Content/common.txt -x "html,php,aspx,jsp" -o feroxbuster.txt
```

![](../../../media/Pasted%20image%2020240908142220.png)

### Artwork

	netplwiz
### OpenNetAdmin



![](../../../media/Pasted%20image%2020240908142416.png)


## Exploitatoin


possível exploit

```
 Exploit Title: Artworks Gallery 1.0 - Arbitrary File Upload RCE (Authenticated)
# Date: November 17th, 2020
# Exploit Author: Shahrukh Iqbal Mirza (@shahrukhiqbal24)
# Vendor Homepage: Source Code & Projects (https://code-projects.org)
# Software Link: https://download.code-projects.org/details/9dfede24-03cc-42a8-b319-f666757ac7cf
# Version: 1.0
# Tested On: Windows 10 (XAMPP Server)
# CVE: CVE-2020-28688
---------------------
Proof of Concept:
---------------------
1. Authenticate as a user (or signup as an artist)
2. Click the drop down for your username and go to My ART+BAY
3. Click on My Artworks > My Available Artworks > Add an Artwork
4. Click on any type of artwork and instead of the picture, upload your php-shell > click on upload
5. Find your shell at 'http://<ip>/<base_url>/pictures/arts/<shell.php>' and get command execution

```


