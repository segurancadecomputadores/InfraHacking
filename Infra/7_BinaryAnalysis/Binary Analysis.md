Binary Analysis
========================

## .net

Podemos utilizar o dnSpy para fazer a engenharia reversa deste tipo de binário. Para isso, podemos seguir o seguinte procedimento:

1. Baixar o binário [dnSpy](https://github.com/dnSpy/dnSpy/releases)
2. Abrir o arquivo a ser analisado com

## Dynamic

Em meio a alguns laboratórios, notei algumas coisas aqui qeu podem ser interessantes quando se trata de análise de binários. Sendo elas alguumas ferramentas como 

strace
ltrace

    ltrace /usr/local/bin/backup a a a

Vale considerar que o comportamento do binário muda conforme o usuário informa argumentos para este.
    
    ltrace /usr/local/bin/backup

Aqui temos a enumeração das chamadas de sistema que o binário faz.

Esse ponto ainda está em construção.


## Ghidra

O ghidra pode ser utilizado para engenharia reversa. No próximo CTF, podemos considerar esse cara.