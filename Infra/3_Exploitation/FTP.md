FTP
========================

Default Creds:

anonymous/anonymous | ftp/ftp | ftpuser|ftpuser

## Enumerating
    nmap -sV -Pn -vv -p 21  --script=ftp-anon,ftp-bounce,ftp-libopie,ftp-proftpd-backdoor,ftp-vsftpd-backdoor,ftp-vuln-cve2010-4221 <hostname>
    
    nmap --script=ftp-anon,ftp-bounce,ftp-libopie,ftp-proftpd-backdoor,ftp-vsftpd-backdoor,ftp-vuln-cve2010-4221,tftp-enum -p 21 <hostname>


## Brute Force
    medusa -h <hostname> -u user -P /root/SecLists/Passwords/bt4-password.txt -M ftp 

    ./root/PWK-Lab/FTP/ftp-user-enum-1.0/ftp-user-enum.pl -U /root/PWK-Lab/fuzzdb/bruteforce/names/simple-users.txt -t <hostname>

