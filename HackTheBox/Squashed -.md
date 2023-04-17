-----

## Initial Information-

### IPs

victim = 10.10.11.191

Date Started:2023-04-15

----
## Initial Recon

- Did the initial scan found that 22, 80 and 11 are open
```shell
nmap  10.10.11.191 
Starting Nmap 7.93 ( https://nmap.org ) at 2023-04-15 11:18 EDT
Nmap scan report for 10.10.11.191
Host is up (0.030s latency).
Not shown: 996 closed tcp ports (reset)
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
111/tcp  open  rpcbind
2049/tcp open  nfs
```

- decided to use showmount -e 10.10.11.191 and found that there is a user ross 
```shell
showmount -e 10.10.11.191 
Export list for 10.10.11.191:
/home/ross    *
/var/www/html *

```


----
## Foothold

---
## Tags

----
## Resources