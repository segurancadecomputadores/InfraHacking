 
## URL based access control (part 7)

Solução:

[Fazendo análise do frontend](../../../Web/2_Enumeration/Web%20Enumeration.md#analisar%20o%20frontend)

Não funcionou:

```
urlExtract https://0a6a00740342adf682db2fc800fe00c8.web-security-academy.net/login
```

![](../../../../media/Pasted%20image%2020240728123627.png)

Nesse cenário, temos de considerar a análise do frontend, que podemos fazer da seguinte maneira:

![](../../../../media/Pasted%20image%2020240728121312.png)

Ao visitar o link em destaque, apertamos F12 no Browser:

![](../../../../media/Pasted%20image%2020240728121423.png)

Deu pra verificar qual a URL na qual o acesso administrativo pode ser alcançado.

Existe uma outra possibilidade de extração desse link:

```
curl -H "Cookie: session=HBM2GnZWIIKHhMfiRJ0LZdAau9mSAJoK" -s https://0a6a00740342adf682db2fc800fe00c8.web-security-academy.net/login | ./extract.rb
```

-s = modol silencioso
-H = adicionar cabeçalho HTTP ou alterar cabeçalho HTTP, se existente.


O cookie deve ser informado visto que temos a sessão do burp Academy para cada laboratório de cada usuário.

![](../../../../media/Pasted%20image%2020240728123152.png)

![](../../../../media/Pasted%20image%2020240728123425.png)

![](../../../../media/Pasted%20image%2020240728123354.png)


## Parameter based access control (part 8)



