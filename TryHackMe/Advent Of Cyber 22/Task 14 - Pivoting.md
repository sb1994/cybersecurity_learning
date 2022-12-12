#### What is Docker?

Docker is a way to package applications, and the associated dependencies into a single unit called an image. This image can then be shared and run as a container, either locally as a developer or remotely on a production server. Santa’s web application and database are running in Docker containers, but only the web application is directly available via an exposed port. A common way to tell if a compromised application is running in a Docker container is to verify the existence of a `/.dockerenv` file at the root directory of the filesystem.

#### What is Metasploit?

Metasploit is a powerful penetration testing tool for gaining initial access to systems, performing post-exploitation, and pivoting to other applications and systems. Metasploit is free, open-source software owned by the US-based cybersecurity firm Rapid7.

### What is a Metasploit session?

After successfully exploiting a remote target with a Metasploit module, a session is often opened by default. These sessions are often Command Shells or Meterpreter sessions, which allow for executing commands against the target. It’s also possible to open up other session types in Metasploit, such as SSH or WinRM - which do not require payloads.

The common Metasploit console commands for viewing and manipulating sessions in Metasploit are:

Metasploit Console Commands

```shell-session
# view sessions
sessions

# upgrade the last opened session to Meterpreter
sessions -u -1

# interact with a session
sessions -i session_id

# Background the currently interactive session, and go back to the Metasploit prompt
background
```

### What is Meterpreter?

Meterpreter is an advanced payload that provides interactive access to a compromised system. Meterpreter supports running commands on a remote target, including uploading/downloading files and pivoting.

Meterpreter has multiple useful commands, such as the following:

Meterpreter Commands

```shell-session
# Get information about the remote system, such as OS
sysinfo

# Upload a file or directory
upload local_file.txt

# Display interfaces
ipconfig

# Resolve a set of host names on the target to IP addresses - useful for pivoting
resolve remote_service1 remote_service2
```

Note that normal command shells do not support complex operations such as pivoting. In Metasploit’s console, you can upgrade the last opened Metasploit session to a Meterpreter session with `sessions -u -1`.

You can identify the opened session types with the `sessions` command. If you are currently interacting with a Meterpreter session, you must first `background` it. In the below example, the two session types are `shell cmd/unix` and `meterpreter x86/linux`:

Meterpreter Commands

```shell-session
msf6 exploit(multi/php/ignition_laravel_debug_rce) > sessions

Active sessions
===============

  Id  Name  Type                   Information                                        Connection
  --  ----  ----                   -----------                                        ----------
  4         shell cmd/unix                                                            10.11.8.17:4444 -> 10.10.152.194:44124 (10.10.152.194)
  5         meterpreter x86/linux  www-data @ 172.28.101.50                           10.11.8.17:4433 -> 10.10.152.194:33296 (172.28.101.50)
        
```

#### What is Pivoting?

Once an attacker gains initial entry into a system, the compromised machine can be used to send additional web traffic through - allowing previously inaccessible machines to be reached.

For example - an initial foothold could be gained through a web application running in a docker container or through an exposed port on a Windows machine. This system will become the attack launchpad for other systems in the network.

![Image of initial foothold between a pentester host and a compromised container](https://tryhackme-images.s3.amazonaws.com/user-uploads/5ed3f13d38407304044dd845/room-content/96b6f34691943493b36baed19bd4641a.png)

We can route network traffic through this compromised machine to run network scanning tools such as `nmap` or `arp` to find additional machines and services which were previously inaccessible to the pentester. This concept is called network pivoting.

![Image of pivoting using a compromised container to other endpoints on the network](https://tryhackme-images.s3.amazonaws.com/user-uploads/5ed3f13d38407304044dd845/room-content/81947d7cc301f1505f383089991cd3bc.png)

