Security Misconfigurations are distinct from the other Top 10 vulnerabilities, because they occur when security could have been configured properly but was not.  

Security misconfigurations include:

-   Poorly configured permissions on cloud services, like S3 buckets
-   Having unnecessary features enabled, like services, pages, accounts or privileges
-   Default accounts with unchanged passwords
-   Error messages that are overly detailed and allow an attacker to find out more about the system
-   Not using [HTTP security headers](https://owasp.org/www-project-secure-headers/), or revealing too much detail in the Server: HTTP header

This vulnerability can often lead to more vulnerabilities, such as default credentials giving you access to sensitive data, XXE or command injection on admin pages.

For more info, I recommend having a look at the [OWASP top 10 entry for Security Misconfiguration](https://owasp.org/www-project-top-ten/OWASP_Top_Ten_2017/Top_10-2017_A6-Security_Misconfiguration)

## Default Passwords

Specifically, this VM focusses on default passwords. These are a specific example of a security misconfiguration. You could, and should, change any default passwords but people often don't.

It's particularly common in embedded and Internet of Things devices, and much of the time the owners don't change these passwords.

It's easy to imagine the risk of default credentials from an attacker's point of view. Being able to gain access to admin dashboards, services designed for system administrators or manufacturers, or even network infrastructure could be incredibly useful in attacking a business. From data exposure to easy RCE, the effects of default credentials can be severe.

In October 2016, Dyn (a DNS provider) was taken offline by one of the most memorable DDoS attacks of the past 10 years. The flood of traffic came mostly from Internet of Things and networking devices like routers and modems, infected by the Mirai malware.

How did the malware take over the systems? Default passwords. The malware had a list of 63 username/password pairs, and attempted to log in to exposed telnet services.

The DDoS attack was notable because it took many large websites and services offline. Amazon, Twitter, Netflix, GitHub, Xbox Live, PlayStation Network, and many more services went offline for several hours in 3 waves of DDoS attacks on Dyn.

## Practical example

This VM showcases a `Security Misconfiguration`, as part of the OWASP Top 10 Vulnerabilities list.

Deploy the VM, and hack in by exploiting the Security Misconfiguration!

---
### Details
ip = 10.10.89.175

- Messed with admin for a little beit and tried to find information in the source code found nothing 
- Wen to github https://github.com/NinjaJc01/PensiveNotes and found the email and password, entered that into the github and found the flag
- ALWAYS think outside the box and to other resources such as github