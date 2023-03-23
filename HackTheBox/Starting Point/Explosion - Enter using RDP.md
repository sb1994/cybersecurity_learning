---- 
## Initial Information

- Attacker IP = 10.129.241.49
- Victim IP = 10.129.241.67
---
## Initial scan

did the nmap scan  it appears to be a windows machine  and has 4 services  running on it  found out that ms-wbt-server  is for RDP so searched for the an RDP client 
```shell
tarting Nmap 7.93 ( https://nmap.org )
Nmap scan report for 10.129.241.67
Host is up (0.018s latency).
Not shown: 996 closed tcp ports (reset)
PORT     STATE SERVICE
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
3389/tcp open  ms-wbt-server

```

didnt have any  usernames provided so used the Administrator  which most people would use as the default for windows server. I looked up the most popular usernames for windows server administrators  to see the most popular

```shell
```
![[Pasted image 20230323000107.png]]
Found the Flag in the flag.txt

--- 

## Resources

Popular usernames for windows server - 
 - https://thwack.solarwinds.com/resources/b/geek-speak/posts/10-usernames-to-avoid-on-your-ftp-server
 - https://www.f5.com/labs/articles/threat-intelligence/spaceballs-security--the-top-attacked-usernames-and-passwords

Msrpc - https://www.extrahop.com/resources/protocols/msrpc/

netbios-ssn - https://miloserdov.org/?p=4261

RDP meaning - https://learn.microsoft.com/en-us/troubleshoot/windows-server/remote/understanding-remote-desktop-protocol

CLI meaning  - https://www.techtarget.com/searchwindowsserver/definition/command-line-interface-CLI#:~:text=A%20command%2Dline%20interface%20(CLI)%20is%20a%20text%2D,interfaces%20and%20character%20user%20interfaces.