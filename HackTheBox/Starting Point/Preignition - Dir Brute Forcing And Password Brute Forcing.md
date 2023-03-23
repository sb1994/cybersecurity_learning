---- 
## Initial Information

- Attacker IP = 10.129.241.49
- Victim IP = 10.129.240.173

---
## Initial scan

Did the initial nmap scan  found that there is a WEbserver running 
```shell

nmap -sV 10.129.240.173  

PORT   STATE SERVICE VERSION
80/tcp open  http    nginx 1.14.2

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.27 seconds

```

Went to the browser to find that there is a home page, clicked around the oage to see if there is anything to click, the links are just for the documentation for nginx
![[Pasted image 20230323001520.png]]
- Checked to see if there is anything hidden in the page, there was nothing 
```html
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="[http://nginx.org/](view-source:http://nginx.org/)">nginx.org</a>.<br/>
Commercial support is available at
<a href="[http://nginx.com/](view-source:http://nginx.com/)">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```
- checked to see if there was anything in the network trafic to see if the webpage connects to a web api, there was none 
![[Pasted image 20230323002545.png]]

- tried to enumerate manually over the url to see if there are other pages or directories in the browser. this took too long so decided to find a tool in kali that could automate this process. Found GoBuster
 - Tried to run it without the wordlist so this request didnt work. Found out the location of it and then the request work and found the admin.php page
 ```shell
gobuster dir -u 10.129.240.173 -w /usr/share/wordlists/dirb/common.txt -x .php

===============================================================
Gobuster v3.5
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.129.240.173
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirb/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.5
[+] Timeout:                 10s
===============================================================
2023/03/22 20:38:24 Starting gobuster in directory enumeration mode
===============================================================
/admin.php            (Status: 200) [Size: 999]
Progress: 4489 / 4615 (97.27%)
===============================================================
2023/03/22 20:38:33 Finished
===============================================================

```

In the browser I used admin admin to find the flag, but I would use a password brute force attack with hydra or John the Ripper

---
Resources

- Gobuster used to enumerate over webserveer directires- 
https://github.com/OJ/gobuster
- Tutorial for using Gobustor - https://www.freecodecamp.org/news/gobuster-tutorial-find-hidden-directories-sub-domains-and-s3-buckets/
- John the ripper - https://www.openwall.com/john/
- Hydra - https://www.kali.org/tools/hydra/