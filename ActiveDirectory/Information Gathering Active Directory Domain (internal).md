Information Gathering Active Directory Domain (internal)
========================


## Caso já temos credencial no domínio

A primeira coisa que é interessante considerarmos para enumeração de domínio na rede é estar com a máquina no domínio, claro. Para isso precisaremos de uma credencial ou ter uma máquina de domínio já comprometida. Caso já temos essa credencial, então devemos utilizá-la da seguinte forma em máquinas windows (que estejam fora do domínio):

    runas /netonly /user:domain.com\username powershell

Exemplo real:

    runas /netonly /user:domain.br\user powershell
    (será solicitado credencial e pode ser informado uma credencial inválida que o comando funcionará, mas por motivos óbvios, quaisquer comando que utilizemos não se autenticará na rede)
    
Dessa forma teremos acesso administrativo por meio de uma console de powershell e enviaremos as credenciais para quaisquer protocolos que utilizaremos

