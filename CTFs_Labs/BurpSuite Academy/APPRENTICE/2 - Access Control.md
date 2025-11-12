
1. [ ] [Verificar robots.txt](#part%04)
2. [ ] [URL que não são previsíveis na aplicação cuja funcionalidades são administrativas](#part%206)
3. [ ] [Controle de acesso via parâmetros da aplicação (Exemplo de cookie)](#part%208)
4. [ ] [Controle de acesso via parâmetro com IDs de usuários](#part%2010)
5. [ ] [Controle de acesso via parâmetro com IDs de usuários 2](#part%2012)

## part 4
### Unprotected admin funcionality

Chamando o laboratório:

![](../../../../media/Pasted%20image%2020240728152529.png)

Chamando o robots.txt:

![](../../../../media/Pasted%20image%2020240728152554.png)

![](../../../../media/Pasted%20image%2020240728152610.png)

![](../../../../media/Pasted%20image%2020240728152623.png)


## part 6
### Unprotected admin functionality with unpredictable URL

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

## part 8
### Parameter based access control

Fazendo a verificação da aplicação após autenticação com as credenciais:

wiener:peter

Foi possível identificar um cookie com "Admin=False", sendo assim, vamos considerar o seguinte cenário:

```
firefox https://<hostname>/admin
```

Interceptar a requisição no burp:

![](../../../../media/Pasted%20image%2020240728153434.png)

Alterando para True:

![](../../../../media/Pasted%20image%2020240728153448.png)


![](../../../../media/Pasted%20image%2020240728153508.png)



![](../../../../media/Pasted%20image%2020240728153541.png)

![](../../../../media/Pasted%20image%2020240728153558.png)

## part 10

### User ID controlled by request parameter, with unpredictable user IDs

```
This lab has a horizontal privilege escalation vulnerability on the user account page, but identifies users with GUIDs.

To solve the lab, find the GUID for carlos, then submit his API key as the solution.

You can log in to your own account using the following credentials: wiener:peter
```

Ao logar na aplicação, achamos a API na primeira página:

![](../../../../media/Pasted%20image%2020240728154131.png)

parando a requisição:

![](../../../../media/Pasted%20image%2020240728154201.png)

Jogamos para o intruder:

![](../../../../media/Pasted%20image%2020240728154258.png)

![](../../../../media/Pasted%20image%2020240728154246.png)


![](../../../../media/Pasted%20image%2020240728154349.png)

Essas tentativas não funcionaram. Então tentaremos outra abordagem.

Ao navegar na aplicação, vamos tentar encontrar o ID do carlos:


![](../../../../media/Pasted%20image%2020240729220245.png)![](../../../../media/Pasted%20image%2020240729220357.png)

Ao clicar no link "carlos", entramos na seguinte tela:
![](../../../../media/Pasted%20image%2020240729220434.png)

Note o ID na URL do carlos:

```
ffccd0af-8518-4530-a11f-bbe3e1269645
```

![](../../../../media/Pasted%20image%2020240729220513.png)

![](../../../../media/Pasted%20image%2020240729220605.png)

![](../../../../media/Pasted%20image%2020240729220621.png)
## part 12

### User ID controlled by request parameter with password disclosure

Ao logar na aplicação, notamos um id na URL:
![](../../../../media/Pasted%20image%2020240729221033.png)
A tentativa inicial seria alterar para administrator:

![](../../../../media/Pasted%20image%2020240729221120.png)

```
adjjsvmyton1r9h9erwe
```

![](../../../../media/Pasted%20image%2020240729221313.png)

"Admin Panel"

![](../../../../media/Pasted%20image%2020240729221330.png)![](../../../../media/Pasted%20image%2020240729221341.png)