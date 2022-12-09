**Learning Objectives**

-   What is Scanning?
-   Scanning types
-   Scanning techniques
-   Scanning tools

**What is Scanning**  

Scanning is a set of procedures for identifying live hosts, ports, and services, discovering the operating system of the target system, and identifying vulnerabilities and threats in the network. These scans are typically automated and give an insight into what could be exploited. Scanning reveals parts of the attack surface for attackers and allows launching targeted attacks to exploit the system.

**Scanning Types**

Scanning is classified as either active or passive based on the degree of intrusiveness to gathering information about a target system or network, as explained below:  

-   **Passive Scanning**: This method involves scanning a network without directly interacting with the target device (server, computer etc.). Passive scanning is usually carried out through packet capture and analysis tools like Wireshark; however, this technique only provides basic asset information like OS version, network protocol etc., against the target.
-   **Active Scanning**: Active scanning is a scanning method whereby you scan individual endpoints in an IT network to retrieve more detailed information. The active scan involves sending packets or queries directly to specific assets rather than passively collecting that data by "catching" it in transit on the network's traffic. Active scanning is an immediate deep scan performed on targets to get detailed information. These targets can be a single endpoint or a network of endpoints.

**Scanning Techniques**

The following standard techniques are employed to scan a target system or network effectively.

**Network Scanning**

A network is usually a collection of interconnected hosts or computers to share information and resources. Network scanning helps to discover and map a complete network, including any live computer or hosts, open ports, IP addresses, and services running on any live host and operating system. Once the network is mapped, an attacker executes exploits as per the target system and services discovered. For example, a computer in a network with an outdated Apache version enables an attacker to launch an exploit against a vulnerable Apache server.

  

**Port Scanning**

Per Wikipedia, "_In computer networking, a port is a number assigned to uniquely identify a connection endpoint and to direct data to a specific service. At the software level, within an operating system, a port is a logical construct that identifies a specific process or a type of network service_".

  

Port scanning is a conventional method to examine open ports in a network capable of receiving and sending data. First, an attacker maps a complete network with installed devices/ hosts like firewalls, routers, servers etc., then scans open ports on each live host. Port number varies between 0 to 65,536 based on the type of service running on the host. Port scanning results fall into the following three categories:

-   **Closed Ports**: The host is not listening to the specific port.
-   **Open Ports**: The host actively accepts a connection on the specific port.
-   **Filtered Ports**: This indicates that the port is open; however, the host is not accepting connections or accepting connections as per certain criteria like specific source IP address.

**Vulnerability Scanning**

The vulnerability scanning proactively identifies the network's vulnerabilities in an automated way that helps determine whether the system may be threatened or exploited. Free and paid tools are available that help to identify loopholes in a target system through a pre-build database of vulnerabilities. Pentesters widely use tools such as [Nessus](https://www.tenable.com/products/nessus) and [Acunetix](https://www.acunetix.com/) to identify loopholes in a system.

**Scanning Tools**

**Network Mapper (Nmap)**

Nmap is a popular tool used to carry out port scanning, discover network protocols, identify running services, and detect operating systems on live hosts. You can learn more about the tool by visiting rooms [Nmap](https://tryhackme.com/room/furthernmap),  [Nmap live host discovery](https://tryhackme.com/room/nmap01), [Nmap basic port scan](https://tryhackme.com/room/nmap02) and [Nmap advanced port scan](https://tryhackme.com/room/nmap03) rooms on TryHackMe.

  

Deploy the virtual machine by clicking `Start Machine` at the top right of this task. This is the machine Recon McRed wants to scan.  

  
You can access the tools needed by clicking the `Start AttackBox` button above. Wait for the AttackBox to load, and launch the terminal from the Desktop. Type `nmap` in the AttackBox terminal.  A quick summary of important Nmap options is listed below:

-   **TCP SYN Scan**: Get the list of live hosts and associated ports on the hosts without completing the TCP three-way handshake and making the scan a little stealthier. Usage: `nmap -sS 10.10.24.250`.

Terminal

```shell-session
mcred@machine$ nmap -sS 10.10.24.250

Starting Nmap 7.60 ( https://nmap.org ) at 2022-11-08 07:05 GMT
Nmap scan report for ip-10-10-170-119.eu-west-1.compute.internal (10.10.170.119)
Host is up (0.0020s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  xxxx
139/tcp open  netbios-ssn
445/tcp open  microsoft-ds
MAC Address: 02:B1:18:36:C7:07 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 1.59 seconds
```

-   **Ping Scan**: Allows scanning the live hosts in the network without going deeper and checking for ports services etc. Usage: `nmap -sn 10.10.24.250`.
-   **Operating System Scan**: Allows scanning of the type of OS running on a live host. Usage: `nmap -O 10.10.24.250`.
-   **Detecting Services**: Get a list of running services on a live host. Usage: `nmap -sV 10.10.24.250`
----

Did an nmap scan on 10.10.24.250 found the following ports:
```shell
nmap -sS 10.10.24.250

Starting Nmap 7.92 ( https://nmap.org ) at 2022-12-04 15:11 EST
Nmap scan report for 10.10.24.250
Host is up (0.011s latency).
Not shown: 996 closed tcp ports (reset)
PORT    STATE SERVICE
22/tcp  open  ssh
80/tcp  open  http
139/tcp open  netbios-ssn
445/tcp open  microsoft-ds

```