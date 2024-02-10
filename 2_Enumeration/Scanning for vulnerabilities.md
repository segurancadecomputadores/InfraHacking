Scanning for vulnerabilities
========================
# SMB

## general

    nmap -v -p 139,445 --script smb-vuln* 192.168.0.0/24
    
## MS-08-067

	nmap --script smb-vuln-ms08-067.nse -p 445 10.102.80.0/24
    
   
	
Esse comando pode derrubar o servidor, então temos que ficar atentos com isso em redes produtivas.

    nmap --script smb-vuln-ms08-067.nse --script-args=unsafe=1 -p 445 10.102.80.0/24
  

## ms-17-010

    nmap -p 445,139 -iL file_with_targets.txt --script smb-vuln-ms17-010


## scanning for vulnerabilities

	nmap --script=vuln host
	
	nmap --script vulscan/vulscan.nse --script-args vulscanoutput=listid
	
	
	nmap --script vulners -sV -sS 10.11.1.250
OBS: É importante ter o -sV para fazer a identificação pela versão do serviço.
	

## RCE vulnerabilities

    smb-vuln-ms08-067.nse,smb-vuln-ms17-010

## just SMB

smb-os-discovery, smb-enum-processes,smb-system-info.nse

## Emumerating NFS

    sudo nmap -sS -p 111,2049 --script nfs-showmount,nfs-ls 10.11.1.72
