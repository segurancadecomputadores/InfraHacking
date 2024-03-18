Binary Analysis
========================

## Dynamic

Em meio a alguns laboratórios, notei algumas coisas aqui qeu podem ser interessantes quando se trata de análise de binários. Sendo elas alguumas ferramentas como 

strace
ltrace

    ltrace /usr/local/bin/backup a a a

Vale considerar que o comportamento do binário muda conforme o usuário informa argumentos para este.
    
    ltrace /usr/local/bin/backup

Aqui temos a enumeração das chamadas de sistema que o binário faz.

Esse ponto ainda está em construção.
