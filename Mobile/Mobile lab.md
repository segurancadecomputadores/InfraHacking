Mobile lab
========================

## Installing mobile lab for tests

### Linux

  1 - Instalei o Genymotion (virtualizador de dispositivos android). Primeiramente devemos instalar o virtualbox, pois é por meio deste que ele virtualiza os dispositivos.


    sudo apt update
    sudo apt install virtualbox
      
 depois, basta baixar e instalar o genymotion:
https://www.genymotion.com/download/

    chomod +x /home/user/Downloads/genymotion_xxx.bin
    sudo ./genymotion_xxx.bin
     
### Windows

Baixar Genymotion junto com virtualbox:

https://www.genymotion.com/download/

![qownnotes-media-iYwRdW](../../media/qownnotes-media-iYwRdW.png)

![qownnotes-media-lwtlfe](../../media/qownnotes-media-lwtlfe.png)

![qownnotes-media-YqLQvw](../../media/qownnotes-media-YqLQvw.png)

![qownnotes-media-qxcNBA](../../media/qownnotes-media-qxcNBA.png)

![qownnotes-media-NeHMFl](../../media/qownnotes-media-NeHMFl.png)

![qownnotes-media-ykSjzu](../../media/qownnotes-media-ykSjzu.png)

Next -> Next -> Next -> install

Criar conta:

![qownnotes-media-QOMzxG](../../media/qownnotes-media-QOMzxG.png)

![qownnotes-media-JstxaM](../../media/qownnotes-media-JstxaM.png)

![qownnotes-media-HjcbCv](../../media/qownnotes-media-HjcbCv.png)

![qownnotes-media-yQkFEA](../../media/qownnotes-media-yQkFEA.png)

![qownnotes-media-xbyGXJ](../../media/qownnotes-media-xbyGXJ.png)

![qownnotes-media-YxVbwh](../../media/qownnotes-media-YxVbwh.png)

![qownnotes-media-hrPnUy](../../media/qownnotes-media-hrPnUy.png)


   **Feito isso, recomendo fazer o download de alguma máquina virtual de API 23+ com Android 8.0 ou 9.0**
   
   instalar o open Gapps  (instala a play store)
   
   ![qownnotes-media-rMNfKS](../../media/qownnotes-media-rMNfKS.png)

   
**Vale considerar que o genymotion utiliza a arquitetura x86 para virtualização e,por isso alguns aplicaitvos podem não funcionar ou não aparecerem na play store quando for pesquisar. Para resolver isso, precisamos instalar um tradutor de arquiteturas que pode ser encontrado em:**

https://github.com/m9rco/Genymotion_ARM_Translation

Quando baixar o ".zip", basta arrastá-lo para a janela da máquina virtual (drag and drop)  
  
source: https://medium.com/@danangtriatmaja/tutorial-genymotion-konfigurasi-burpsuite-ssl-certificate-dengan-adb-indonesian-1a3e9427429f

  1.2 In kali linux or any other linux, make the certificate valid for Android:
  
![qownnotes-media-tZLzhn](file://media/19012.png)

	openssl x509 -inform DER -in cacert.der -out cacert.pem
	openssl x509 -inform PEM -subject_hash_old -in cacert.pem | head -n 1

The last command will show the hash of the certificate. Than rename the file with the hash as a name:
	mv cacert.pem output_of_last_command<cert>.0
  
  
  1.3 Instalar o certificado do Burp como "System" no dispositivo:
  
	# adb remount
	# adb push <cert>.0 /sdcard/
	# adb shell
	# mv /sdcard/<cert>.0 /system/etc/security/cacerts/
	# chmod 644 /system/etc/security/cacerts/<cert>.0
	# reboot
	
#### Obsoleto #####
  3 - Baixar o Xposed Installer (https://xposed-installer.en.uptodown.com/android) e instalá-lo
  http://dl-xda.xposed.info/framework/ (se for de versões diferentes)
  (documentação para instalação do Xposed https://forum.xda-developers.com/showthread.php?t=3034811)
  4 - Instalar o JustTrustMe ( https://github.com/Fuzion24/JustTrustMe/releases/tag/v.2)
  5 - Apartir daqui já é possível interceptar as informações trafegadas entre a aplicação e o dispositivo móvel.
  
  
### Debugging smali code
 2 . Instalar o Android studio. Baixando da internet.
  2.1 Baixar o plugin smalidea por meio da seguinte URL:
  https://bitbucket.org/JesusFreke/smali/downloads/

Documentation:
https://github.com/JesusFreke/smalidea
  
![qownnotes-media-yjwjOq](file://media/25621.png)

 2.2 Instalar o plugin no Android studio: File -> Settings -> Plugins -> (engrenagem) -> Install from Disk...

![qownnotes-media-gNxroc](file://media/10156.png)

Selecionar o zip baixado do passo anterior e reiniciar a IDE.


Analisanndo código da aplciação de forma estática
   1 - Desta vez etou utilizando o Qark para verificar se este é interessante (get clone https://github.com/linkedin/qark)
   
   2 - Online scanner for static analysis
   
   https://www.ostorlab.co/scan/mobile

Analisando dinâmicamente o códig da aplicação:
   Baixar o Drozer (https://labs.mwrinfosecurity.com/tools/drozer/)
   
   https://github.com/mwrlabs/drozer/releases/download/2.3.4/drozer-agent-2.3.4.apk
   Pode até surgir um problema de dependência, que é facilmente corrigido por: apt-get -f install
   O drozer tem um agente que é instalado no dispositivo também que pode ser baixado no mesmo link acima.

Para eventuais problemas de dependência, uma das soluçõe sencontradas foi baixar o wlh do python realizando os seguintes passos:

1 - download:

- wget https://github.com/mwrlabs/drozer/releases/download/2.4.4/drozer-2.4.4-py2-none-any.whl
  2 - pip install drozer-2.4.4-py2-none-any.whl

![qownnotes-media-DsDZHV](file://media/30424.png)

 
   xposed install in android
   https://forum.xda-developers.com/showthread.php?t=3034811
   
   
   https://pentester.land/tips-n-tricks/2018/10/19/installing-arm-android-apps-on-genymotion-devices.html
   
## Drozer

commands:

	drozer console connect --s <IP_SERVER_DROZER_AGENT>
	
## Verify if an application is debbugable

	run app.package.debuggable
	
## verify Android SDK

	grep -r "ro.build.version.sdk=" /system/build.prop

	adb shell getprop ro.product.cpu.abi