Initial acess
========================


### Payload Development

|Name|Description|URL|
|-----|-----------|----|
|Ivy|Ivy is a payload creation framework for the execution of arbitrary VBA (macro) source code directly in memory.|https://github.com/optiv/Ivy|
|PEzor|Open-Source PE Packer|https://github.com/phra/PEzor|
|GadgetToJScript|A tool for generating .NET serialized gadgets that can trigger .NET assembly load/execution when deserialized using BinaryFormatter from JS/VBS/VBA scripts.|https://github.com/med0x2e/GadgetToJScript|
|ScareCrow|Payload creation framework designed around EDR bypass.|https://github.com/optiv/ScareCrow|
|Donut|Donut is a position-independent code that enables in-memory execution of VBScript, JScript, EXE, DLL files and dotNET assemblies. |https://github.com/TheWover/donut|
|Mystikal|macOS Initial Access Payload Generator|https://github.com/D00MFist/Mystikal|
|charlotte|c++ fully undetected shellcode launcher ;)|https://github.com/9emin1/charlotte|
|InvisibilityCloak|Proof-of-concept obfuscation toolkit for C# post-exploitation tools. This will perform the below actions for a C# visual studio project.|https://github.com/xforcered/InvisibilityCloak|
|Dendrobate|Dendrobate is a framework that facilitates the development of payloads that hook unmanaged code through managed .NET code.|https://github.com/FuzzySecurity/Dendrobate|
|Offensive VBA and XLS Entanglement|This repo provides examples of how VBA can be used for offensive purposes beyond a simple dropper or shell injector. As we develop more use cases, the repo will be updated.|https://github.com/BC-SECURITY/Offensive-VBA-and-XLS-Entanglement|
|xlsGen|Tiny Excel BIFF8 Generator, to Embedded 4.0 Macros in *.xls|https://github.com/aaaddress1/xlsGen|
|darkarmour|Windows AV Evasion|https://github.com/bats3c/darkarmour|
|InlineWhispers|Tool for working with Direct System Calls in Cobalt Strike's Beacon Object Files (BOF)|https://github.com/outflanknl/InlineWhispers|
|EvilClippy|A cross-platform assistant for creating malicious MS Office documents. Can hide VBA macros, stomp VBA code (via P-Code) and confuse macro analysis tools. Runs on Linux, OSX and Windows.|https://github.com/outflanknl/EvilClippy|
|OfficePurge|VBA purge your Office documents with OfficePurge. VBA purging removes P-code from module streams within Office documents.|https://github.com/fireeye/OfficePurge|
|ThreatCheck|Identifies the bytes that Microsoft Defender / AMSI Consumer flags on.|https://github.com/rasta-mouse/ThreatCheck|
|CrossC2|Generate CobaltStrike's cross-platform payload|https://github.com/gloxec/CrossC2|
|Ruler|Ruler is a tool that allows you to interact with Exchange servers remotely, through either the MAPI/HTTP or RPC/HTTP protocol.|https://github.com/sensepost/ruler|
|DueDLLigence|Shellcode runner framework for application whitelisting bypasses and DLL side-loading. The shellcode included in this project spawns calc.exe.|https://github.com/fireeye/DueDLLigence|
|RuralBishop|RuralBishop is practically a carbon copy of UrbanBishop by b33f, but all P/Invoke calls have been replaced with D/Invoke.|https://github.com/rasta-mouse/RuralBishop|
|TikiTorch|TikiTorch was named in homage to CACTUSTORCH by Vincent Yiu. The basic concept of CACTUSTORCH is that it spawns a new process, allocates a region of memory, then uses CreateRemoteThread to run the desired shellcode within that target process. Both the process and shellcode are specified by the user.|https://github.com/rasta-mouse/TikiTorch|
|SharpShooter|SharpShooter is a payload creation framework for the retrieval and execution of arbitrary CSharp source code. SharpShooter is capable of creating payloads in a variety of formats, including HTA, JS, VBS and WSF.|https://github.com/mdsecactivebreach/SharpShooter|
|SharpSploit|SharpSploit is a .NET post-exploitation library written in C#|https://github.com/cobbr/SharpSploit|
|MSBuildAPICaller|MSBuild Without MSBuild.exe|https://github.com/rvrsh3ll/MSBuildAPICaller|
|macro_pack|macro_pack is a tool by @EmericNasi used to automatize obfuscation and generation of MS Office documents, VB scripts, and other formats for pentest, demo, and social engineering assessments.|https://github.com/sevagas/macro_pack|
|inceptor|Template-Driven AV/EDR Evasion Framework|https://github.com/klezVirus/inceptor|
|mortar|evasion technique to defeat and divert detection and prevention of security products (AV/EDR/XDR)|https://github.com/0xsp-SRD/mortar|
|ProtectMyTooling|Multi-Packer wrapper letting us daisy-chain various packers, obfuscators and other Red Team oriented weaponry. Featured with artifacts watermarking, IOCs collection & PE Backdooring. You feed it with your implant, it does a lot of sneaky things and spits out obfuscated executable.|https://github.com/mgeeky/ProtectMyTooling|

### Staging


|Name|Description|URL|
|-----|-----------|----|
|pwndrop|Self-deployable file hosting service for red teamers, allowing to easily upload and share payloads over HTTP and WebDAV.|https://github.com/kgretzky/pwndrop|
|C2concealer|A command line tool that generates randomized C2 malleable profiles for use in Cobalt Strike.|https://github.com/FortyNorthSecurity/C2concealer|
|FindFrontableDomains|Search for potential frontable domains|https://github.com/rvrsh3ll/FindFrontableDomains|
|Domain Hunter|Checks expired domains for categorization/reputation and Archive.org history to determine good candidates for phishing and C2 domain names|https://github.com/threatexpress/domainhunter|
|RedWarden|Flexible CobaltStrike Malleable Redirector|https://github.com/mgeeky/RedWarden|
|AzureC2Relay|AzureC2Relay is an Azure Function that validates and relays Cobalt Strike beacon traffic by verifying the incoming requests based on a Cobalt Strike Malleable C2 profile.|https://github.com/Flangvik/AzureC2Relay|
|C3|C3 (Custom Command and Control) is a tool that allows Red Teams to rapidly develop and utilise esoteric command and control channels (C2).|https://github.com/FSecureLABS/C3|
|Chameleon|A tool for evading Proxy categorisation|https://github.com/mdsecactivebreach/Chameleon|
|Cobalt Strike Malleable C2 Design and Reference Guide|Cobalt Strike Malleable C2 Design and Reference Guide|https://github.com/threatexpress/malleable-c2/|
|redirect.rules|Quick and dirty dynamic redirect.rules generator|https://github.com/0xZDH/redirect.rules|
|CobaltBus|Cobalt Strike External C2 Integration With Azure Servicebus, C2 traffic via Azure Servicebus|https://github.com/Flangvik/CobaltBus|
|SourcePoint|SourcePoint is a C2 profile generator for Cobalt Strike command and control servers designed to ensure evasion.|https://github.com/Tylous/SourcePoint|
|RedGuard|RedGuard is a C2 front flow control tool,Can avoid Blue Teams,AVs,EDRs check.|https://github.com/wikiZ/RedGuard|