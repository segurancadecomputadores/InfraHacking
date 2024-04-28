Scripts
========================

## Username generator from web page

```  
cat usergen.py 
#!/usr/bin/python

import sys

def help():
    print("Usage: python usergen.py <file name>")
    exit()

#def remove_spaces(name):

def capitalize_first_letter(name):
    out = name.capitalize()
    out = out.replace(" ", "")
    return out

def first_letter_and_last_name(name):
    out = name[0]
    out = out + name.split()[-1]
    return out

def first_letter_and_last_name_all_lower(name):
    out = name.lower()
    return first_letter_and_last_name(out)

def username_generator(name):

    print(capitalize_first_letter(name))
    print(first_letter_and_last_name(name))
    print(first_letter_and_last_name_all_lower(name))
        


##############################################
## MAIN                                     ##
##############################################
if(sys.argv[1]):
    try:
        f = open(sys.argv[1], "r")
    except:
        help()

    temp = f.read().splitlines()
    for name in temp:
        username_generator(name)
else:
    help()
```

## Basic Fuzzer HTTP

## Python http upload server

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