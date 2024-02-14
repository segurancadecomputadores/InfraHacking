# README

Vamos colocar as possibilidades de cada cenário neste README.

## Kernel exploitation 

1 - Enumerar o sistema

Aqui temos poucas peculiaridadeswinPEASx64.exe, visto que basta pesquisarmos um exploit para o sistema a ser atacado. As informações e exploits podem ser obtidas por meio do exploit-db ou até pesquisas no google. Sem novidade.

## User privileges
2 - User privileges

Aqui a peculiridade é do Windows. Buscamos por SEImpersonate. Basicamente pode ser explorado pelo JuicyPotatoNG.

## Automatic Enumeration

3 - Automatização aqui que devemos nos atentar quanto aos testes e enumerações que ele faz. Vamos considerar a princípio o WINPEAS.

3.1 - System information

![qownnotes-media-nXSTLG](../.gitbook/assets/qownnotes-media-nXSTLG.png)

3.2 All hotfixes ( KBs ) e Patches
3.3 Environment variables
3.4 Senhas em registro
3.5 Powershell history commands
3.6 Drives information
3.7 AllwaysInstallElevated
3.8 Users Enumeration
3.9 device drivers
3.10 Network Shares
3.11 Serviços que estão rodando
3.12 Creds saved in browsers

Não é certeza que utilizando somente essa ferramenta seria o suficiente para automatizar todo o processo de enumeração, porém, temos de considerar principalmente a questão de navegar dentre os diretórios para conseguir ter uma visão melhor do sistema.

## DirectoryEnumeration

Temos que considerar arquivos de emails para ver se tem alguma credencial salva lá ou serviços escondidos, como no caso do arquivo /etc/apache2/sites-enabled/*, subdomínios possíveis que existam nesses serviços.

Além disso, devemos considerar possíveis binários que são passíveis de exploração, pesquisando nas pastas principais das máquinas.

## Senhas

Aqui são várias possibilidades que podemos considerar, sendo elas:

Pasword spray, usuário=senhas, enumeração via informações obtidas por meio de certificados ou a enumeração do prórpio sistema, como o systeminfo, por exemplo.

Procurar por senhas disponívels em arquivos de configuração ou que arquivos que constam nos diretórios dos usuários, por exemplo.

Aqui devemos considerar pastas como:

recursivamente

/home
/home/user

Pensar em possíveis arquivos onde possam ser encontradas credenciais.

C:\users
c:\users \username (desconsiderar somente o appdata)

## Serviços

Aqui é interessante olharmos para serviços que não estão expostos para a interface externa. Lembrando que devemos considerar ou serviços em localhost ou serviços cujo portas estão expostas, mas bloqueadas pelo firewall

