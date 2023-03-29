---- 
## Initial Information

- Attacker IP = 10.129.241.49
- Victim IP = 10.129.243.241
---
## Initial scan

Did the intial nmap scan , found the following:

```shell
nmap -sV 10.129.243.241  

Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-25 09:11 EDT
Nmap scan report for 10.129.243.241
Host is up (0.037s latency).
Not shown: 999 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
80/tcp open  http    nginx 1.14.2

```
We found that there is a WebServer running.

Given that there is web server, I decided to access the web page using ip but this redirects to http://ignition.htb/. but this gives errors.
![[Pasted image 20230325132459.png]]
Based on previous experience I decided to add the ip and domain to the hosts file so that they can understand each other 
```text
**./etc/hosts**

127.0.0.1	localhost
127.0.1.1	kali
::1		localhost ip6-localhost ip6-loopback
ff02::1		ip6-allnodes
ff02::2		ip6-allrouters

#Hack the Box 
10.129.243.241 ignition.htb

```
this showed the actual page that was meant to be seen
![[Pasted image 20230325133602.png]]
this appears to be a CMS application.

- Decided to do gobuster inorder to see the structure the of the file system and see if there are any hidden directory. 
- Found that there is a hidden admin  directory that leads to a magento login form
```shell
gobuster dir -u http://ignition.htb -w /usr/share/woxt -x .php
========================================================
Gobuster v3.5
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@fire
========================================================
[+] Url:                     http://ignition.htb
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirb/c
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.5
[+] Extensions:              php
[+] Timeout:                 10s
========================================================
2023/03/25 10:01:18 Starting gobuster in directory enume
========================================================
/0                    (Status: 200) [Size: 25803]
/admin                (Status: 200) [Size: 7092]
/catalog              (Status: 302) [Size: 0] [--> http://ignition.htb/]                                        
/checkout             (Status: 302) [Size: 0] [--> http://ignition.htb/checkout/cart/]                          
/cms                  (Status: 200) [Size: 25817]
/contact              (Status: 200) [Size: 28673]
/enable-cookies       (Status: 200) [Size: 27176]
/errors               (Status: 301) [Size: 185] [--> http://ignition.htb/errors/]   
```
- Went to the admin form and looked up the requirements for magento and found that the passwords. this only requires 7 chars( number and letters)
- decided to test them one by one using the `admin` username. found that I could login using the admin and qwerty123 password in the list 
![[Pasted image 20230325142011.png]]

I found the flag on  the admin home page.

----
## Resources
- Requierments for magento passwords. https://experienceleague.adobe.com/docs/commerce-admin/systems/security/security-admin.html?lang=en
- Most popular passwords 2023 -https://cybernews.com/best-password-managers/most-common-passwords/