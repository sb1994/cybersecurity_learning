-----

## Initial Information-

### IPs

- Victim - 10.129.198.87

Date Started:2023-04-05

----
## Initial Recon

- Did the initial scan found that the following 
```shell
nmap -sV 10.129.198.87 
PORT     STATE SERVICE         VERSION                                                                              
22/tcp   open  ssh             OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)                         
6789/tcp open  ibm-db2-admin?                                                                                       
8080/tcp open  http-proxy                                                                                           
8443/tcp open  ssl/nagios-nsca Nagios NSCA  
```
- Opened the request in burpsuite repeater inorder manipulate the request and listened to tcp dump inorder to see what is returned 
![[Pasted image 20230406115451.png]]
```shell
# TCP dump to listen for the ldap request
sudo tcpdump -i tun0 port 1389
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on tun0, link-type RAW (Raw IP), snapshot length 262144 bytes
06:56:31.274856 IP 10.129.198.87.59024 > 10.10.14.43.1389: Flags [S], seq 2241667316, win 64240, options [mss 1340,sackOK,TS val 580227324 ecr 0,nop,wscale 7], length 0
06:56:31.274871 IP 10.10.14.43.1389 > 10.129.198.87.59024: Flags [R.], seq 0, ack 2241667317, win 0, length 0

```

- then went to the github to get the recommeneded shell exploit https://github.com/veracode-research/rogue-jndi

----
## Foothold

---
## Tags
#Web
#VulnerabilityAssessment
#Databases
#Injection
#CustomApplications
#OutdatedSoftware
#MongoDB
#Java

Reconnaissance

Clear Text Credentials

Default Credentials

Code Injection
----
## Resources