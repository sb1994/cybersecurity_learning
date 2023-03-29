## Basic Details

kali IP: 192.198.10.5
Victim Metasploitable: 192.198.10.5

### 1.  Recon the victim
- Ping the machine to insure that we have a connection to it 
```shell 

ping 192.198.10.4

PING 192.198.10.4 (192.198.10.4) 56(84) bytes of data.
64 bytes from 192.198.10.4: icmp_seq=1 ttl=64 time=1.40 ms
64 bytes from 192.198.10.4: icmp_seq=2 ttl=64 time=0.855 ms
64 bytes from 192.198.10.4: icmp_seq=3 ttl=64 time=0.998 ms
64 bytes from 192.198.10.4: icmp_seq=4 ttl=64 time=0.862 ms
64 bytes from 192.198.10.4: icmp_seq=5 ttl=64 time=1.01 ms
64 bytes from 192.198.10.4: icmp_seq=6 ttl=64 time=0.826 ms
64 bytes from 192.198.10.4: icmp_seq=7 ttl=64 time=1.13 ms
^C
--- 192.198.10.4 ping statistics ---
7 packets transmitted, 7 received, 0% packet loss, time 6012ms
rtt min/avg/max/mdev = 0.826/1.011/1.399/0.187 ms


```
Given that there was no lose of packet from the icmp ping we can confirm that we can connect to the victim machine.

-  Scan the victim machine with Nmap to find open ports services 
```shell
nmap -sV 192.198.10.4


Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-16 08:31 EDT
Nmap scan report for 4-10-198-192.dynamic.knett.com.ar (192.198.10.4)
Host is up (0.0052s latency).
Not shown: 977 closed tcp ports (conn-refused)
PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         vsftpd 2.3.4
22/tcp   open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
23/tcp   open  telnet      Linux telnetd
25/tcp   open  smtp        Postfix smtpd
53/tcp   open  domain      ISC BIND 9.4.2
80/tcp   open  http        Apache httpd 2.2.8 ((Ubuntu) DAV/2)
111/tcp  open  rpcbind     2 (RPC #100000)
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
512/tcp  open  exec?
513/tcp  open  login
514/tcp  open  shell?
1099/tcp open  java-rmi    GNU Classpath grmiregistry
1524/tcp open  bindshell   Metasploitable root shell
2049/tcp open  nfs         2-4 (RPC #100003)
2121/tcp open  ftp         ProFTPD 1.3.1
3306/tcp open  mysql       MySQL 5.0.51a-3ubuntu5
5432/tcp open  postgresql  PostgreSQL DB 8.3.0 - 8.3.7
5900/tcp open  vnc         VNC (protocol 3.3)
6000/tcp open  X11         (access denied)
6667/tcp open  irc         UnrealIRCd
8009/tcp open  ajp13       Apache Jserv (Protocol v1.3)
8180/tcp open  http        Apache Tomcat/Coyote JSP engine 1.1
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port514-TCP:V=7.93%I=7%D=3/16%Time=64130C28%P=x86_64-pc-linux-gnu%r(NUL
SF:L,48,"\x01Couldn't\x20get\x20address\x20for\x20your\x20host\x20\(5-10-1
SF:98-192\.dynamic\.knett\.com\.ar\)\n");
Service Info: Hosts:  metasploitable.localdomain, irc.Metasploitable.LAN; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 65.35 seconds


```