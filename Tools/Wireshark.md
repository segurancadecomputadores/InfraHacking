Wireshark
========================

Boolean expressions:

* Equals: == or eq
* And: && or and
* Or: || (double pipe) or or
* Different !=
	
Examples:

 1. ip.addr eq 192.168.10.195 and ip.addr == 192.168.10.1
 2. http.request && ip.addr == 192.168.10.195
 3. http.request || http.response
 4. dns.qry.name contains microsoft or dns.qry.name contains windows

## tcp payloads filter

    tcp contains "Password" #Case sensitive

## web based filters:

	http.request or ssl.handshake.type == 1

The matches, or ~, operator makes it possible to search for text in string fields and byte sequences using a regular expression, using Perl regular expression syntax. Note: Wireshark needs to be built with libpcre in order to be able to use the matches operator.

Match HTTP requests where the last characters in the uri are the characters "gl=se":

	http.request.uri matches "gl=se$"
or
	http.request.uri contains "login" || http.request.uri contains "Login"
	
opposite of contains:
	http.request.method == "POST" and not (http.request.uri contains "/api/tsdb/query")


only POST requests:

	http.request.method == "POST"
	
	
## Others

### ip filters

different 

	ip.addr != 10.43.54.65
	!(ip.src == 10.43.54.65 or ip.dst == 10.43.54.65)

 	ip.addr == 10.43.54.65
is equivalent to
 	ip.src == 10.43.54.65 or ip.dst == 10.43.54.65
This can be counterintuitive in some cases. Suppose we want to filter out any traffic to or from 10.43.54.65. We might try the following:

 	ip.addr != 10.43.54.65
which is equivalent to
 	ip.src != 10.43.54.65 or ip.dst != 10.43.54.65
This translates to "pass all traffic except for traffic with a source IPv4 address of 10.43.54.65 and a destination IPv4 address of 10.43.54.65", which isn't what we wanted.

Instead we need to negate the expression, like so:

 	! ( ip.addr == 10.43.54.65 )
which is equivalent to
	
	!(ip.src == 10.43.54.65 or ip.dst == 10.43.54.65)


 	tcp.port eq 25 or icmp
 
 ip.src==192.168.0.0/16 and ip.dst==192.168.0.0/16
 
### tcp and udp filters
 
  	tcp.window_size == 0 && tcp.flags.reset != 1
  
  	smb || nbns || dcerpc || nbss || dns
   
   Match packets containing the (arbitrary) 3-byte sequence 0x81, 0x60, 0x03 at the beginning of the UDP payload, skipping the 8-byte UDP header. Note that the values for the byte sequence implicitly are in hexadecimal only. (Useful for matching homegrown packet protocols.)

	udp[8:3]==81:60:03
  
  It is also possible to search for characters appearing anywhere in a field or protocol by using the contains operator.

Match packets that contains the 3-byte sequence 0x81, 0x60, 0x03 anywhere in the UDP header or payload:

	udp contains 81:60:03
Match packets where SIP To-header contains the string "a1762" anywhere in the header:

	sip.To contains "a1762"
  
  Gotchas
Some filter fields match against multiple protocol fields. For example, "ip.addr" matches against both the IP source and destination addresses in the IP header. The same is true for "tcp.port", "udp.port", "eth.addr", and others. It's important to note that

### arp filters

arp.dst.hw_mac == 34:64:a9:09:c9:85