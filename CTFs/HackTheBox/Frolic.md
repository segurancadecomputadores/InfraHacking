Frolic
========================

## Enumeration

    rpcclient -N -U '' 10.10.10.111

    sudo nmap -sU -p 53,161,111,137,139,500,2049 -Pn frolic.htb | tee nmap_udp_output.txt

![qownnotes-media-KopeCx](../../../media/qownnotes-media-KopeCx.png)



    smbclient -U admin //10.10.10.111/IPC$
 
![qownnotes-media-MHiwrb](../../../media/qownnotes-media-MHiwrb.png)

    sudo nmap_enum frolic.htb | tee nmap_output.txt

![qownnotes-media-mXVkYF](../../../media/qownnotes-media-mXVkYF.png)


    sudo nmap -p- -Pn -T5 -n frolic.htb | tee nmap_fullportstcp_output.txt

![qownnotes-media-bQzeLG](../../../media/qownnotes-media-bQzeLG.png)

![qownnotes-media-siPyTX](../../../media/qownnotes-media-siPyTX.png)

    crackmapexec smb 10.10.10.111 -u admin -p "imnothuman"