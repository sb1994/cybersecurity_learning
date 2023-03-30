
----
### Initial IPs

Attacker = 10.10.14.136
Victim = 10.129.248.31

---

## Initial Scan

- Nmap scan shows a service running on port 8080
```shell
nmap -sC -sV 10.129.248.31
Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-29 18:35 EDT
Nmap scan report for 10.129.248.31
Host is up (0.021s latency).
Not shown: 999 closed tcp ports (reset)
PORT     STATE SERVICE VERSION
8080/tcp open  http    Jetty 9.4.39.v20210325
|_http-server-header: Jetty(9.4.39.v20210325)
| http-robots.txt: 1 disallowed entry 
|_/
|_http-title: Site doesn't have a title (text/html;charset=utf-8).


```
----
## Resources

--- 
## Tags

#Web
#CommonApplications
#Jenkins
#Recon
#RemoteCodeExecution
#DefaultCreds
