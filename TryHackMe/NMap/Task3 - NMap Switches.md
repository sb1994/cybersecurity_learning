
# Workflow

1. Load the Attack Machine
2. Use the help flag to learn more about nmap 

```shell
sudo nmap - h
OR
sudo man nmap

```
3. Inorder to do a UDP scan need the following:
	```shell
 sudo nmap -sU
nmap -sS '10.10.135.81'
```

4. In order to scan for the OS need the following:
```shell
nmap -O '10.10.135.81'	
```
5. Get the version of the services:
  ```shell
 nmap -sV 10.10.135.81
```
![[nmap_sv.PNG]]
6. Increase the verbosity of the of an nmap scan means more English words:
```shell
nmap -vv 10.10.135.81
```
![[nmap_vv.PNG]]
### Outputting Nmap scans to files

1. Output nmap result into 3 major formats at same time:  
	```shell
 nmap -oA output 10.10.135.81 "Will add to formats"
 nmap -vv output 10.10.135.81 > output.txt
```
![[Pasted image 20221110212132.png]]
For labs need to remember to create a directory to  store the the files i.e. ***/Desktop/lab01*** 
2. output in `normal` format:
	```shell
	nmap -oN output 10.10.135.81
```
3. Output in `grepable` format:
```shell
	nmap -oG output 10.10.135.81
```

### NMap Port Scanning 
1. Scan single port on 80:
```shell
	nmap -p 80 output 10.10.135.81
```
![[Pasted image 20221110220105.png]]
2. Scan range of ports on 1000-1500:
```shell
	nmap -p 1000-1500 output 10.10.135.81
```
![[Pasted image 20221110220419.png]]
3. Scan range of ports on 1000-1500:
```shell
	nmap -p 1000-1500 10.10.135.81
```
4. Scan ALL ports:
```shell
	nmap -p- 10.10.135.81
```
![[Pasted image 20221110221012.png]]


