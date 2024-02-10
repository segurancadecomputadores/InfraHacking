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

