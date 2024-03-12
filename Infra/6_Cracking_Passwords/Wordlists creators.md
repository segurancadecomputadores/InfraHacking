Wordlists creators
========================

Primeiro descompactar a principal wordlist do kali linux

    gzip -d /usr/share/wordlists/rockyou.txt.gz

## cewl

Gera wordlists a partir de um site.

    cewl www.megacorpone.com -m 6 -w megacorp-cewl.txt
Onde -m significa mínimo de 6 caracteres
e -w é no arquivo a ser escrito a wordlist gerada

    wc -l megacorp-cewl.txt
    grep Nano megacorp-cewl.txt

## Crunch

sources:
https://pentestblog.in/kali-linux/how-to-create-wordlist-with-crunch-in-kali-linux-2021/


    crunch 6 6 12345b -o word.txt

-t = Specified pattern of the generated passwords.

, = Uppercase

@ = Lowercase

^ = Special character

% = Numeric value

    crunch 9 9 -t ,@@@@^%%% | more

## cupp

projeto: https://github.com/Mebus/cupp




usuario | senha
---|---
root|a3tNR@bn5Ce
vivtools|vivtools
VIVZ2APPO38|VIVZ2APPO38
admin|admin
AmdocsHML|vivo31HML
coreDefUser11
Unix11!
VIVACM58|VIVACM58
VIVDB58|VIVDB58
VIVEBL58
Jeeadmin|Jeeadmin1
RepSA|Unix11
VIVREFO58|VIVREFO58
AIM_DBA|VIVREFO58
telegraf|metricsmetricsmetricsmetrics
user1|secret
username|pa$$word
rodrigo.alves|Fiest@2008
jenkins|Inv3R$aw%$54
automata|inFLux@00
cloudadmin|aeShesas7Mah5rej
Administrator|SKIbet1@_45#


## John the ripper

com o ssh2john você preparar o arquivo id_rsa para bruteforce do john offline:

    sudo python /usr/share/john/ssh2john.py /home/acosta/ssh_james.txt > crack.txt

    john --wordlist=wordlist.lst --rules --stdout > mutated.txt

source:
https://www.tunnelsup.com/getting-started-cracking-password-hashes/

john --wordlist=password.lst hashes-3.des.txt

 - pratical example
	john --format=nt --wordlist=wordlist.txt hashes.txt

OBS: Once we got some hashes with responder, we can go to /usr/share/responder/logs and take these files to crack the hashes obtained with the following command:
	
	john --format=netntlmv2 --wordlist=wordlist.lst HTTP-NTLMv2-10.12.23.100.txt

	john --incremental hashes-3.des.txt

**OBS important to note that the files have to be us-ascii or utf-8 encoded**

### Tutorial de regras
source:https://www.openwall.com/john/doc/RULES.shtml
https://www.gracefulsecurity.com/custom-rules-for-john-the-ripper/
https://www.gracefulsecurity.com/custom-rules-for-john-the-ripper-examples/

Editando este arquivo, podemos inserir regras para mutar a a wordlist e assim conseguir uma wordlist melhorada:

    sudo nano /etc/john/john.conf

### String commands

AN"STR"	insert string STR into the word at position N
To append a string, specify "z" for the position. To prefix the word with a string, specify "0" for the position.

Although the use of the double-quote character is good for readability, you may use any other character not found in STR instead. This is particularly useful when STR contains the double-quote character. There's no way to escape your quotation character of choice within a string (preventing it from ending the string and the command), but you may achieve the same effect by specifying multiple commands one after another. For example, if you choose to use the forward slash as your quotation character, yet it happens to be found in a string and you don't want to reconsider your choice, you may write "Az/yes/$/Az/no/", which will append the string "yes/no". Of course, it is simpler and more efficient to use, say, "Az,yes/no," for the same effect.

### Simple commands.
:	no-op: do nothing to the input word
l	convert to lowercase
u	convert to uppercase
c	capitalize
C	lowercase the first character, and uppercase the rest
t	toggle case of all characters in the word
TN	toggle case of the character in position N
r	reverse: "Fred" -> "derF"
d	duplicate: "Fred" -> "FredFred"
f	reflect: "Fred" -> "FredderF"
{	rotate the word left: "jsmith" -> "smithj"
}	rotate the word right: "smithj" -> "jsmith"
$X	 append character X to the word
^X	prefix the word with character X

Adiciona ao final da palavra um dígito
Az"[0-9]"
A0"[0-9]"
A de append
z posição final da palavra (no caso do início da palavra seria o "0")

    john --wordlist=wordlist.lst --rules --stdout > mutated.txt