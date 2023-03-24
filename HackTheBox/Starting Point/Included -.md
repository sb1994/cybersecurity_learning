----
### IPs
Attacker = 10.10.14.136
Victim = 10.129.95.185

----

- Did an initial Nmap scan
```shell
Nmap scan report for 10.129.95.185
Host is up (0.031s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.43 seconds

```
- Did a Nmap scan for UDP found tftp
```shell
PORT      STATE         SERVICE
68/udp    open|filtered dhcpc
69/udp    open|filtered tftp
123/udp   open|filtered ntp
5093/udp  open|filtered sentinel-lm
5351/udp  open|filtered nat-pmp
16711/udp open|filtered unknown
17490/udp open|filtered unknown
19120/udp open|filtered unknown
19141/udp open|filtered unknown
19500/udp open|filtered unknown
19933/udp open|filtered unknown
20206/udp open|filtered unknown
49165/udp open|filtered unknown
49226/udp open|filtered unknown
54094/udp open|filtered unknown
59193/udp open|filtered unknown


```
- did a gobuster scan to see if there are any hidden folder
```shell
gobuster dir -u 10.129.95.185 -w /usr/share/wordlists/dirb/common.txt
===============================================================
Gobuster v3.5
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.129.95.185
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirb/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.5
[+] Timeout:                 10s
===============================================================
2023/03/23 21:27:25 Starting gobuster in directory enumeration mode
===============================================================
/.hta                 (Status: 403) [Size: 278]
/.htpasswd            (Status: 403) [Size: 278]
/.htaccess            (Status: 403) [Size: 278]
/fonts                (Status: 301) [Size: 314] [--> http://10.129.95.185/fonts/]
/images               (Status: 301) [Size: 315] [--> http://10.129.95.185/images/]
/index.php            (Status: 301) [Size: 308] [--> http://10.129.95.185/]
/server-status        (Status: 403) [Size: 278]
Progress: 4511 / 4615 (97.75%)
===============================================================
2023/03/23 21:27:34 Finished
======================================
```

