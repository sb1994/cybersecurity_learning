---- 
## Initial Information

- Attacker IP = 10.10.14.136
- Victim IP = 10.129.176.129
---
## Initial Recon 
- Did the initial scan and found that port 22 and 80 are open meaning that there is **ssh access** and an **express http server** running based on the results. this is also a **Ubuntu server** as the version of ssh shows unbuntu in the results
```shell
nmap -sV 10.129.176.129

Nmap scan report for 10.129.176.129

Host is up (0.029s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.4 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Node.js (Express middleware)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```
- did a gobuster scan in order to see any hidden directires, didn't find anything 
```shell

gobuster dir -u 10.129.97.64 -w /usr/share/wordlists
========================================================
Gobuster v3.5
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@fire
========================================================
[+] Url:                     http://10.129.97.64
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirb/c
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.5
[+] Timeout:                 10s
========================================================
2023/03/26 09:42:23 Starting gobuster in directory enume
========================================================
/css                  (Status: 301) [Size: 173] [--> /cs
/images               (Status: 301) [Size: 179] [--> /im
/js                   (Status: 301) [Size: 171] [--> /js
Progress: 4602 / 4615 (99.72%)
========================================================
2023/03/26 09:42:40 Finished


```
- visted the home page of the express server with the victim IP found the following page.
![[Pasted image 20230325143531.png]]
- Enumerated some urls such as register, login and admin. there are no hidden URLS
- Tested for server side template injection, got the following error when testing with {{7`*`7}} got the following error
```json
	"Error: Parse error on line 1:"
1	" {{7*7}}"
2	"---^"
3	"Expecting 'ID', 'STRING', 'NUMBER', 'BOOLEAN', 'UNDEFINED', 'NULL', 'DATA', got 'INVALID'"
4	"    at Parser.parseError (/root/Backend/node_modules/handlebars/dist/cjs/handlebars/compiler/parser.js:268:19)"
5	"    at Parser.parse (/root/Backend/node_modules/handlebars/dist/cjs/handlebars/compiler/parser.js:337:30)"
6	"    at HandlebarsEnvironment.parse (/root/Backend/node_modules/handlebars/dist/cjs/handlebars/compiler/base.js:46:43)"
7	"    at compileInput (/root/Backend/node_modules/handlebars/dist/cjs/handlebars/compiler/compiler.js:515:19)"
8	"    at ret (/root/Backend/node_modules/handlebars/dist/cjs/handlebars/compiler/compiler.js:524:18)"
9	"    at router.post (/root/Backend/routes/handlers.js:15:18)"
10	"    at Layer.handle [as handle_request] (/root/Backend/node_modules/express/lib/router/layer.js:95:5)"
11	"    at next (/root/Backend/node_modules/express/lib/router/route.js:137:13)"
12	"    at Route.dispatch (/root/Backend/node_modules/express/lib/router/route.js:112:3)"
13	"    at Layer.handle [as handle_request] (/root/Backend/node_modules/express/lib/router/layer.js:95:5)"
```

----
### Resources

- Server side template injection -  https://book.hacktricks.xyz/pentesting-web/ssti-server-side-template-injection
- 