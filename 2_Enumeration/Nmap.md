---
Title: Hacking>Nmap
Content: Nmap scanning
Tag: Hacking>Nmap
...
---
Nmap
========================

Here we have to identify OS, default scripts, services version
Basic commands
nmap -O -A -sU -sV -Pn --webxml -oX output_file.xml (For UDP scan)
nmap -O -A -sV -Pn --webxml -oX output_file.xml (For TCP scan)

Nmap Reference and examples:
nmap -p 1-65535 -sV -sS -T4 target -> Full port TCP Syn scan (-sS), T4 timing (more acurate than T5) with service detection (-sV)
nmap -v -sS -A -T5 target -> Default nmap port scan with scripts executed against targets, -T5 timing (not too much accurate)
nmap -v -sV -O -sS -T5 target -> (-O) OS detection, default port scan Syn stealth (-sS), (-v) verbose
nmap -sn -iL file_with_targets                                     -> ping scan a (-iL) file with a list of IPs one by line
nmap -Pn -iL file_with_targets                                    -> (-Pn) skip ping scan (treat all targets as online)

## Host Discovery

![qownnotes-media-oaPMbH](file://media/24161.png)


## Scan Techniques

![qownnotes-media-fRivnZ](file://media/31667.png)

Reports
-oG greapable report (not usable)
-oA all reports form (xml report ok for powershell parsing csv)

Commands executed
nmap -Pn --webxml -oX nmap_apdata_test_webxml -sV -iL urls.txt
nmap -Pn -sU --webxml -oX nmap_apdata_test_webxml -sV -iL urls.txt

ACK scan
nmap -Pn -sA -iL urls.txt (returned all filtered)

Syn stealth scan
nmap --webxml -oX nmap_apdata_infra -sV -A -O -iL ../0_Supporting_Files/ips.txt

## waf detection with nmap

nmap -p80,443 --script http-waf-detect --script-args="http-waf-detect.aggro,http-waf-detect.detectBodyChanges" 13.111.49.191

## output


### html output
    nmap -O -A -sU -sV -Pn --webxml -oX off_sec_lab_nmap.xml (For UDP scan)
    xsltproc off_sec_lab_nmap.xml -o off_sec_lab_nmap.html
    
### CSV
ref: https://sevenlayers.com/index.php/296-nmap-xml-to-csv

    git clone https://github.com/NetsecExplained/Nmap-XML-to-CSV.git
    xml2csv.py -f off_sec_lab_nmap -csv output.csv
    
Exemplo prático:

[Lab OSCP](Lab.md#Enumeration )

   
### xml

    nmap -O -A -sU -sV -Pn --webxml -oX off_sec_lab_nmap.xml
    
    