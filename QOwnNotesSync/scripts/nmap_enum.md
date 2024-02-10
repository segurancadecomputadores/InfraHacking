# nmap\_enum

```
#!/bin/bash

ip=$1

nmap --open -n -Pn -sS $ip | cut -d '/' -f 1 | grep -E '[0-9]{1,5}$'| grep -v Nmap > default_nmap_open_tcp_ports.txt
echo " " >> default_nmap_open_tcp_ports.txt
echo "Open ports: "
cat default_nmap_open_tcp_ports.txt

for i in $(cat default_nmap_open_tcp_ports.txt):
do
        echo "Valor de i = $i"
        if [ "$i" == '21' ]
        then
                nmap -sS -sV -p $i --script "ftp* and not brute" -n $ip
        fi

        if [ "$i" == '22' ]
        then
                nmap -sS -sV -p $i --script "ssh* and not brute" -n $ip
        fi

        if [ "$i" == '25' ]
        then
                nmap -sS -sV -p $i --script "smtp*" -n $ip
        fi

        if [ "$i" == '80' ]
        then
                nmap -sS -sV -p $i --script "http* and not http-brute and not http-slowloris* and not http-form-fuzzer" -n $ip
                dirb http://$ip -o dirb_80.txt
                nikto -h http://$ip -Tuning x 6 -o nikto_80.html -Format htm
                sudo -u acosta firefox http://$ip http://$ip/robots.txt http://$ip/sitemap.xml nikto_80.html &
        fi

        if [ "$i" == '139' ]
        then
                nmap -sS -sV  -p $i --script "rpc*" -n $ip
                ##########################
                ## NULL session test    ##
                ##########################
                rpcclient -N -U '' $ip
        fi

        if [ "$i" == '445' ]
        then
                nmap -sS -sV  -p $i,139 --script "smb* and not brute and not smb-flood" -n $ip
                nbtscan -r $ip/32
                enum4linux -a -A $ip
        fi

        if [ "$i" == '389' ]
        then
                nmap -sS -sV  -p $i --script "ldap* and not brute" -n $ip
                echo #############################
                echo ## EXECUTAR LDAPSEARCH     ##
                echo #############################
        fi


        if [ "$i" == '3389' ]
        then
                nmap -sS -sV  -p $i --script "vulners, rdp*" -n $ip
        fi

        if [ "$i" == '443' ]
        then
                nmap -sS -sV -p $i --script "http* or ssl* and not http-brute and not http-slowloris* and not http-form-fuzzer" -n $ip
                dirb https://$ip -o dirb_443.txt
                nikto -h https://$ip -Tuning x 6 -o nikto_443.html -Format htm
                sudo -u acosta firefox https://$ip https://$ip/robots.txt https://$ip/sitemap.xml nikto_443.html &
        fi

        if [ "$i" == '636' ]
        then
                nmap -sS -sV  -p $i --script "ldap* and not brute" -n $ip
        fi

done

```
