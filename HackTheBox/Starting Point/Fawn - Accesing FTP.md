---- 
## Initial Information

- Attacker IP = 10.129.241.49
- Victim IP = 10.129.241.49
---
## Initial Scanning 


- Did an nmap scan to find what services are running found that FTP is running on port 21
```shell
nmap 10.129.241.49            

Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-22 17:43 EDT
Nmap scan report for 10.129.241.49
Host is up (0.035s latency).
Not shown: 999 closed tcp ports (conn-refused)
PORT   STATE SERVICE
21/tcp open  ftp


```

- found that it is running vsftpd 3.0.3 and that it is running a unix based system and that it has a very high chance of being a Linux  system
```shell
nmap -sV 10.129.241.49
Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-22 17:46 EDT
Nmap scan report for 10.129.241.49
Host is up (0.036s latency).
Not shown: 999 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
Service Info: OS: Unix

```


```shell
sudo nmap -O 10.129.241.49
[sudo] password for kali: 
Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-22 17:50 EDT
Nmap scan report for 10.129.241.49
Host is up (0.018s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE
21/tcp open  ftp
Aggressive OS guesses: Linux 5.0 (95%), Linux 5.0 - 5.4 (95%), Linux 5.4 (94%), HP P2000 G3 NAS device (93%), Linux 4.15 - 5.6 (93%), Linux 5.3 - 5.4 (93%), Linux 2.6.32 (92%), Linux 2.6.32 - 3.1 (92%), Ubiquiti AirMax NanoStation WAP (Linux 2.6.32) (92%), Linux 3.7 (92%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops

OS detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 5.44 seconds

```

- Logged into the Victim Server using FTP using the "anonymous" username and no password we know we were successful as we got a code 220
```shell
ftp 10.129.241.49 
Connected to 10.129.241.49.
220 (vsFTPd 3.0.3)
Name (10.129.241.49:kali): anonymous
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||12445|)
150 Here comes the directory listing.
-rw-r--r--    1 0        0              32 Jun 04  2021 flag.txt
226 Directory send OK.
ftp> 

```

- Got the flag with the ftp Get command  to download into my download
```shell
get flag.txt
``` 