Responder
========================

## Output do responder

Responder logs:

/usr/share/responder/logs/*.txt*

sadailton::GRUPOAMIL:df7c1141e65a3d27:EB85706CB640283617EF2D3233F05B13:0101000000000000305539E10664D5018AEAA2CC7A6900B7000000000200060053004D0042000100160053004D0042002D0054004F004F004C004B00490054000400120073006D0062002E006C006F00630061006C000300280073006500720076006500720032003000300033002E0073006D0062002E006C006F00630061006C000500120073006D0062002E006C006F00630061006C00080030003000000000000000010000000020000098D8979A8C08E34A366BEF07E8FC19520FAB3439C52DC7B6BFDB07F575A8EDEB0A0010000000000000000000000000000000000009001A0048005400540050002F007400720061006400750074006F0072000000000000000000

onde :

username:user_id:domínio:CHALLENGE:LM_HASH:NT_HASH

## Método de ataque
O Responder é uma ferramenta utilizada para realizar um ataque de spoof nos protocolos LLMNR e NetBios. Um ataque de spoof é quando um atacante se passa por um outro host legítimo da rede, mascarando suas intenções maliciosas.

## Conceitos básicos
LLMNR é um protocolo utilizado para tradução de nomes por máquinas Windows. Quando a resolução de DNS não funciona, é o LLMNR que o Windows utiliza para realizar a tradução de nomes e posteriormente o NetBios.

Em um cenário real, algumas condições devem ser obedecidas para elaboração do ataque. São elas:

	-> Estar na mesma rede local da vítima
	-> LLMNR e NetBios habilitado na máquina da vítima (já vem habilitado por default no Windows)

Utilizando como exemplo o erro de digitação do usuário ao consultar um compartilhamento de rede, o que vai ocasionar em uma falha de resolução de DNS, segue o seguinte esquema de ataque:
	
![qownnotes-media-aQDtig](file://media/28272.png)

	1. Vítima envia um nome incorreto de nome de compartilhamento de rede para resolução de DNS
	2. DNS Server responde NOT FOUND
	3. A máquina da vítima envia em broadcast o LLMNR e NetBios para resolução do nome
	4. responder envia para o cliente que o nome que ele procura está relacionado ao IP da máquina do atacante
	5. A máquina da vítima envia suas credenciais para o atacante, usuário e hash de senha
	6. Responder responde a máquina da vítima com um erro, tornando o ataque transparente pra ela.

A partir desse momento o atacante já possui o hash de senha da vítima e pode utilizar um swervidor para quebrar esse hash de senha, por exemplo ou até mesmo executar um ataque de SMB Relay.

## Ataque na prática

Responder rodando na tentativa de "spoofar" alguma requisição LLMNR, NETBIOS ou mDNS. Para isso, ele simula vários serviços online, como HTTP, SMB, LDAP, dentre outros: 

![media-puipyA](file://media/8393.png)

Cliente faz uma requisição de DNS que não será resolvida, devido a um erro de digitação (ao invés de SHARE01, ele digita SNARE01):


![media-LUZzQo](file://media/6512.png)


O atacante, estando na mesma rede que essa vítima, irá "spoofar" a requisição via LLMNR, devido ao fato de a resolução de DNS dar errado:


![media-fBXgwd](file://media/9815.png)


O responder retorna um erro ao usuário.

![media-mYHsXX](file://media/26937.png)


Aqui o atacante já possui o hash de senha para tentativa de quebra da mesma. Ou ele pode partir para o próximo ataque que é explicado abaixo.


SMB Relay
=====================================

## Método de ataque
Uma vez adquirido um hash de senha válido de algum cliente dentro da rede local, temos a opção de tentar quebrá-lo para obter a senha em texto claro ou repassá-lo para tentativa de autenticação em algum servidor.

## Condições de exploração
	-> O servidor deve estar com a assinatura SMB desabilitada
	-> Deve se estar na mesma rede local da vítima para obte o hash de senha e tentar o replay par ao servidor alvo, seja por um ataque man-in-the-middle ou pelo ataque do responder
	
Por meio de ferramentas de man-in-the-middle como "arpspoof" , "ettercap" ou cain e abel , por exemplo, é possível a obtenção de hashes de senha.

**Preparando o ataque**

exploit/multi/handler

Com esse módulo do metasploit, vamos subir um listener para o payload gerado na próxima etapa:

![qownnotes-media-hbElit](file://media/32252.png)


Agora devemos gerar um payload para execução no servidor alvo:

![qownnotes-media-zlVljN](file://media/19963.png)


e informar como parâmetro para o smbrelayx.py:

![qownnotes-media-kaxfuI](file://media/16088.png)


Daí, olha lá como faz ó:

![qownnotes-media-scQNPO](file://media/24114.png)


LINDO!

a Shell está na mão... Agora é só usar a criatividade e correr para o abraço.
