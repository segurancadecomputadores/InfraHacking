Nikto
========================
De maneira geral, utilizamos o seguinte comando como exemplo para todos os casos:

    nikto -h $ip -port $port -Tuning x 6
    
Pensando um pouco em evasao, podemos considerar o seguinte:

    nikto -Option USERAGENT="Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:108.0) Gecko/20100101 Firefox/108.0" -h $ip -port $port -Tuning x 6

Para fins de customizacaco e aprendizado, podemos utilizar um proxy:

    nikto -Option PROXYHOST=127.0.0.1 -Option PROXYPORT=8080 -h 10.11.1.31 -useproxy -h $ip -port $port -Tuning x 6

O demais sao cheat sheet:

    nikto -host [host IP/name]


    nikto -host [host IP/name] -port [port number 1], [port number 2], [port number 3]

    nikto -host [host IP/name] -output [output_file]

    nikto -host [host IP/name] -useproxy [proxy address]
    
    ### Evasion Options

Nikto -h <Hostname/IP> -evasion <Option>
1 Random URI Encoding
2 Directory Self-Reference /./
3 Premature URL ending
4 Prepend long random string
5 Fake parameter
6 TAB as request spacer
7 Change the case of the URL
8 Used windows directory separator \
A Use a carriage return (0x0d) as a request spacer
B  Use binary value (0x0b) as a request spacer

#### Changing user agent in nikto

	vi /etc/nikto.config

![qownnotes-media-ELkVvO](file://media/3472.png)


#### Output File Format

Nikto -h <Hostname/IP> -Format <Option>
csv       Comma-separated-value
htm    HTML Format
msf+  Log to Metaspoloit
nbe     Nessus NBE
txt       Plain text
xml    XML Format

#### Saving results
You can output to a file with the -o option
You can specify the format of the output file with -Format [csv htm txt or xml]

eg to perform an SQL injection test and save results to an html file with verbose output for your terminal:

	nikto -Display V -o results.html -Format htm -Tuning 9 -h example.com

#### Tuning

Nikto -h <Hostname/IP> -Tuning <Option>
1   Interesting file
2   Misconfiguration
3   Information Disclosure
4   Injection (XSS/Script/HTML)
5   Remote File Retrieval – Inside Web Root
6   Denial of Service
7   Remote File Retrieval – Server Wide
8   Command Execution – Remote Shell
9   SQL Injection
0   File Upload
a   Authentication Bypass
b   Software Identification
c   Remote Source Inclusion
x   Reverse Tuning Option

So, to only perform an SQL injection test against your target:

	nikto -Tuning 9 -h example.com
or to run everything except DOS

	nikto -Tuning x 6 -h example.com