Burpsuite
========================
## Burpsuite configuration

    #!/bin/bash
    
    java -noverify -javaagent:/media/acosta/owncloud/Tools_Utilities/tools/Burpsuite_Pro/BurpSuite_java8_pro/BurpSuiteLoader_v2021.8.jar -jar /media/acosta/owncloud/Tools_Utilities/tools/Burpsuite_Pro/BurpSuite_java8_pro/burpsuite_pro_v2021.8.jar


## Bypass cloudflare human verification

Proxy -> Options -> Match and replace

Adiconar a regra sinalizada pela flecha

![qownnotes-media-aRFydf](../../media/qownnotes-media-aRFydf.png)



## Run burp 2020.4

java.exe -noverify -javaagent:BurpSuiteLoader.jar -jar burpsuite_pro_v2020.4.jar

on linux

    cd /media/acosta/owncloud/Tools_Utilities/tools/windows/java-jdk14/jdk-14.0.1/bin
    java -noverify -javaagent:BurpSuiteLoader_v2020.7.jar -jar burpsuite_pro_v2020.7.jar


## Run Burp Suite with 2 GB RAM, which is more than the default

	java -jar -Xmx2g Burp\ Suite\ Free\ Edition.app/Contents/java/app/burpsuite_free.jar
	
	
### Fisrt of all, install jython:

The standalone file is at <burpsuite_root_directory>/BurpPlugins/jython-standalone-2.7.0

configuring as the following:

![qownnotes-media-tmDqKS](../../media/13931.png)


### Then, install the extesions



#### Installing CO2

![qownnotes-media-iAutUn](../../media/29574.png)

#### Installing sqlmap integrator

In kali linux Machine, execute the following command:

    sqlmapapi -H <ip_to_bind> -p <port_to_bind> -s
    
![qownnotes-media-AScISA](../../media/28861.png)


![qownnotes-media-jqebtE](../../media/26149.png)


#### Beautifier

#### 

#### Logger++

This extension is very useful for mapping web applications. some actions are required:

Before installation of this extender, this button will be "Install'

![qownnotes-media-LPwihN](file://media/24492.png)

Depois de instalar, para mapear todos os parâmetros adicione a coluna "Response Body"

![qownnotes-media-vMcUjt](file://media/18998.png)


### SSL ciphers suite problem (solved)


This is not actually from his video. But, after successfully installing Burp Suite, I encountered browser HTTPS errors because the Burp proxy and the target website (yahoo.com) could not agree on a cipher suite. Here's some packet detail that I grabbed with Wireshark. It shows yahoo's webserver offering my browser (which was actually the Burp proxy) a reasonable choice of three strong cipher suites. But Burp wasn't able to support any of them. I'm guessing this was due to the US government's well-known restrictions on exporting cryptographic technology.

Cipher Suite: TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (0xc02f)

Cipher Suite: TLS_RSA_WITH_AES_128_GCM_SHA256 (0x009c)

Cipher Suite: TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (0xc030)
I easily solved the problem by downloading Oracle's Java Cryptography Extension (JCE) Unlimited Strength Jurisdiction Policy Files 8, as suggested on these two pages:

http://cybergibbons.com/security-2/burp-suite/missing-tls-ciphers-when-running-burp-suite/

http://stackoverflow.com/questions/37741142/how-to-install-unlimited-strength-jce-for-java-8-in-os-x

After installing the JCE and restarting Burp, the problem was gone.

### Runnning authenticated scan with burpsuite

source: https://forum.portswigger.net/thread/authenticated-scanning-34381c9fcc1ea

https://portswigger.net/support/configuring-burp-suites-session-handling-rules


### Running burp suite in kali linux

First, we have to know that the version of java is 8, update (at maximum) 241.

if error ocurr during the initalization of burp, try:
	
	update-alternatives --config java
	
![qownnotes-media-zrqIie](file://media/15074.png)


