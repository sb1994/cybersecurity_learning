
What Are Log Files and Why Are They Useful

Log files are files that contain historical records of events and other data from an application. Some common examples of events that you may find in a log file:  

-   Login attempts or failures
-   Traffic on a network
-   Things (website URLs, files, etc.) that have been accessed
-   Password changes
-   Application errors (used in debugging)
-   _and many, many more_

By making a historical record of events that have happened, log files are extremely important pieces of evidence when investigating:

-   What has happened?
-   When has it happened?
-   Where has it happened?
-   Who did it? Were they successful?
-   What is the result of this action?

For example, a systems administrator may want to log the traffic happening on a network. We can use logging to answer the questions above in a given scenario:  

_A user has reportedly accessed inappropriate material on a University network._ With logging in place, a systems administrator could determine the following:

Question

Answer

**What has happened?**  

A user is confirmed to have accessed inappropriate material on the University network.  

**When has it happened?**  

It happened at 12:08 on Tuesday, 01/10/2022.  

**Where has it happened?**  

It happened from a device with an IP address (an identifier on the network) of 10.3.24.51.  

**Who did it? Were they successful?**  

The user was logged into the university network with their student account.  

**What is the result of the action?**  

The user was able to access _inappropriatecontent.thm_.

  

### What Does a Log File Look Like?!

Log files come in all shapes and sizes. However, a useful log will contain at least some of the following attributes:

1.  A timestamp of the event (I.e. Date & Time)
2.  The name of the service that is generating the logfile (I.e. SSH is a remote device management protocol that allows a user to login into a system remotely)
3.  The actual event the service logs (i.e., in the event of a failed authentication, what credentials were tried, and by whom? (IP address)).  
    

![an annotated picture of an example of a log file](https://tryhackme-images.s3.amazonaws.com/user-uploads/5de96d9ca744773ea7ef8c00/room-content/8953b78dae6d4c5755a4f145247f5adb.png)  

  

## Common Locations of Log Files

### Windows

- Windows features an in-built application that allows us to access historical records of events that happen. The Event Viewer is illustrated in the picture below:

![a picture of the event viewer on Windows](https://tryhackme-images.s3.amazonaws.com/user-uploads/5de96d9ca744773ea7ef8c00/room-content/50ae2577dcec3b3462b13c6225ba111d.png)  

These events are usually categorised into the following:
- **Category**
- **Description**
- **Example**
- **Application**

This category contains all the events related to applications on the system. For example, you can determine when services or applications are stopped and started and why.

The service "tryhackme.exe" was restarted.

**Security**

This category contains all of the events related to the system's security. For example, you can see when a user logs in to a system or accesses the credential manager for passwords.

User "cmnatic" successfully logged in.

**Setup**

This category contains all of the events related to the system's maintenance. For example, Windows update logs are stored here.

The system must be restarted before "KB10134" can be installed.

**System**

This category contains all the events related to the system itself. This category of events contains logs that relate to changes in the system itself. For example, when the system is powered on or off or when devices such as USB drives are plugged-in or removed.

The system unexpectedly

### Linux (Ubuntu/Debian)

- On this flavour of Linux, operating system log files (and often software-specific such as apache2) are located within the `/var/log` directory. We can use the `ls` in the `/var/log` directory to list all the log files located on the system:

Listing log files within the /var/log directory

```shell-session
cmnatic@aoc2022-day-2:/var/log$ ls -lah
total 724K
drwxrwxr-x   9 root      syslog          4.0K Nov 14 10:59 .
drwxr-xr-x  13 root      root            4.0K Oct 26  2020 ..
drwxr--r-x   3 root      root            4.0K Nov 14 10:56 amazon
drwxr-xr-x   2 root      root            4.0K Oct 26  2020 apt
-rw-r-----   1 syslog    adm              11K Nov 14 11:03 auth.log
-rw-rw----   1 root      utmp               0 Oct 26  2020 btmp
-rw-r--r--   1 root      root            7.3K Nov 14 10:59 cloud-init-output.log
-rw-r--r--   1 syslog    adm             251K Nov 14 10:59 cloud-init.log
drwxr-xr-x   2 root      root            4.0K Oct  7  2020 dist-upgrade
-rw-r--r--   1 root      adm              36K Nov 14 10:59 dmesg
-rw-r--r--   1 root      adm              36K Nov 14 10:56 dmesg.0
-rw-r--r--   1 root      root             12K Oct 26  2020 dpkg.log
drwxr-sr-x+  3 root      systemd-journal 4.0K Nov 14 10:55 journal
-rw-r-----   1 syslog    adm              98K Nov 14 10:59 kern.log
drwxr-xr-x   2 landscape landscape       4.0K Nov 14 10:57 landscape
-rw-rw-r--   1 root      utmp            286K Nov 14 11:03 lastlog
drwx------   2 root      root            4.0K Nov 14 10:55 private
-rw-r-----   1 syslog    adm             207K Nov 14 11:03 syslog
drwxr-x---   2 root      adm             4.0K Nov 14 10:55 unattended-upgrades
-rw-rw-r--   1 root      utmp            8.3K Nov 14 11:03 wtmp
```

The following table highlights some important log files:

Category

Description

File (Ubuntu)

### Example

#### Authentication

This log file contains all authentication (log in). This is usually attempted either remotely or on the system itself (i.e., accessing another user after logging in).

**auth.log**

Failed password for root from 192.168.1.35 port 22 ssh2.  

#### Package Management

This log file contains all events related to package management on the system. When installing a new software (a package), this is logged in this file. This is useful for debugging or reverting changes in case this installation causes unintended behaviour on the system.

**dpkg.log**

2022-06-03 21:45:59 installed neofetch.

#### Syslog

This log file contains all events related to things happening in the system's background. For example, crontabs executing, services starting and stopping, or other automatic behaviours such as log rotation. This file can help debug problems.

**syslog**

2022-06-03 13:33:7 Finished Daily apt download activities..

#### Kernel

This log file contains all events related to kernel events on the system. For example, changes to the kernel, or output from devices such as networking equipment or physical devices such as USB devices. 

**kern.log**

2022-06-03 10:10:01 Firewalling registered

## Looking Through Log Files

Log files can quickly contain many events and hundreds, if not thousands, of entries. The difficulty in analysing log files is separating useful information from useless. Tools such as Splunk are software solutions known as Security Information and Event Management (SIEM) is dedicated to aggregating logs for analysis. Listed in the table below are some of the advantages and disadvantages of these platforms:

  
**Disadvantage**

SIEM platforms are dedicated services for log analysis.

Commercial SIEM platforms are expensive to license and run.

SIEM platforms can collect a wide variety of logs - from devices to networking equipment.

SIEM platforms take considerable time to properly set up and configure.

SIEM platforms allow for advanced, in-depth analysis of many log files at once.

SIEM platforms require training to be properly used.

Luckily for us, most operating systems already come with a set of tools that allow us to search through log files. In this room, we will be using the `grep` command on Linux.

### Grep 101

Grep is a command dedicated to searching for a given text in a file. _Grep_ takes a given input (a text or value) and searches the entire file for any text that matches our input. 

Before using `grep`, we have to find the location of the log file that we want to search for. By default, `grep` will use your current working directory. You can find out what your current working directory is by using `pwd`. For example, in the terminal below, we are in the working directory _/home/cmnatic/aoc2022/day2/:_

Using pwd to view our current working directory

```shell-session
cmnatic@thm:~/aoc2022/day2 pwd
           /home/cmnatic/aoc2022/day2/
```

If we wish to change our current working directory, you can use `cd` followed by the new path you wish to change to. For example, `cd /my/path/here`. Once we've determined that we are in the correct directory, we can use `ls` to list the files and directories in our current working path. An example of this has been put into the terminal below:  

Using ls to list the files and directories in our current directory

```shell-session
cmnatic@aoc2022-day-2:~$ ls -lah
webserver.log helloworld.txt mydirectory
```

Now that we know where our log files are, we can begin to proceed with learning how to use `grep`. To use grep, we need to do three things:

-   Call the command.
-   Specify any options that we wish to use (this will later be explained), but for now, we can ignore this.
-   Specify the location of the file we wish to search through (`grep` will first assume the file is in your current directory unless you tell it otherwise by providing the path to the file i.e. _/path/to/our/logfile.log_).

For example, in the terminal below, we are using `grep` to look through the log file for an IP address. The log file is located in our current working directory, so we do not need to provide a path to the log file - just the name of the log file.  

Using grep to look in a log file for activity from an IP address

```shell-session
ubuntu@thm:~ grep "192.168.1.30" access.log
192.168.1.30 - - [14/Nov/2022:00:53:07 +0000] "GET / HTTP/1.1" 200 13742
192.168.1.30 - - [14/Nov/2022:00:53:43 +0000] "HEAD
```

In the terminal above, we can see two entries in this log file (access.log) for the IP address "192.168.1.30". For reference, we've narrowed down two entries from a log file with 469 entries. Our life has already been made easier! Here are some ideas for things you may want to use grep to search a log file for:  

-   A name of a computer.
-   A name of a file.
-   A name of a user account.
-   An IP address.
-   A certain timestamp or date.

As previously mentioned, we can provide some options to `grep` to enable us to have more control over the results of grep. The table below contains some of the common options that you may wish to use with `grep`.

--- 
### Details
-   IP address: 10.10.195.226
-   Username: elfmcblue
-   Password: tryhackme!


1.  sshed into the machine
```shell
ssh elfmcblue@10.10.195.226
```

2. listed the files in the is the initial directory:
```shell
ls

SSHD.log  webserver.log

```
3. Found that the data was taken by 10.10.249.191 on 8/Nov/2022:12
```shell
less webserver.log 

10.10.249.191 - - [18/Nov/2022:12:35:27 +0000] "GET /list31026894265468 HTTP/1.1" 404 437 "-" "gobuster/3.0.1"
10.10.249.191 - - [18/Nov/2022:12:35:27 +0000] "GET /list21026892699625 HTTP/1.1" 404 437 "-" "gobuster/3.0.1"
10.10.249.191 - - [18/Nov/2022:12:35:27 +0000] "GET /list41026894282906 HTTP/1.1" 404 437 "-" "gobuster/3.0.1"
10.10.249.191 - - [18/Nov/2022:12:35:27 +0000] "GET /list51026894325718 HTTP/1.1" 404 437 "-" "gobuster/3.0.1"
10.10.249.191 - - [18/Nov/2022:12:35:27 +0000] "GET /pricelists HTTP/1.1" 404 437 "-" "gobuster/3.0.1"
10.10.249.191 - - [18/Nov/2022:12:35:27 +0000] "GET /price_list HTTP/1.1" 404 437 "-" "gobuster/3.0.1"
10.10.249.191 - - [18/Nov/2022:12:35:27 +0000] "GET /maillist_signin HTTP/1.1" 404 437 "-" "gobuster/3.0.1"
10.10.249.191 - - [18/Nov/2022:12:35:27 +0000] "GET /0,18266,pga-pgamoneylist,00 HTTP/1.1" 404 437 "-" "gobuster/3.0.1"
10.10.249.191 - - [18/Nov/2022:12:35:27 +0000] "GET /0,,pga-pgamoneylist,00 HTTP/1.1" 404 437 "-" "gobuster/3.0.1"


```

3. ran a few grep commands but couldn't anything until i entered santa, and found the list 
```shell
grep -r "santa"
webserver.log:10.10.249.191 - - [18/Nov/2022:12:28:16 +0000] "GET /santa HTTP/1.1" 404 437 "-" "gobuster/3.0.1"
webserver.log:10.10.249.191 - - [18/Nov/2022:12:28:17 +0000] "GET /santa_claus HTTP/1.1" 404 437 "-" "gobuster/3.0.1"
webserver.log:10.10.249.191 - - [18/Nov/2022:12:28:17 +0000] "GET /evilsanta HTTP/1.1" 404 437 "-" "gobuster/3.0.1"
webserver.log:10.10.249.191 - - [18/Nov/2022:12:28:18 +0000] "GET /santana HTTP/1.1" 404 437 "-" "gobuster/3.0.1"
webserver.log:10.10.249.191 - - [18/Nov/2022:12:28:18 +0000] "GET /santabarbara HTTP/1.1" 404 437 "-" "gobuster/3.0.1"
webserver.log:10.10.249.191 - - [18/Nov/2022:12:34:39 +0000] "GET **/santaslist.txt HTTP**/1.1" 200 133872 "-" "Wget/1.19.4 (linux-gnu)"
webserver.log:10.10.249.191 - - [18/Nov/2022:12:35:18 +0000] "GET /santafe HTTP/1.1" 404 437 "-" "gobuster/3.0.1"
webserver.log:10.10.249.191 - - [18/Nov/2022:12:35:18 +0000] "GET /jasonsantamar-20 HTTP/1.1" 404 437 "-" "gobuster/3.0.1"
webserver.log:10.10.249.191 - - [18/Nov/2022:12:35:18 +0000] "GET /santa_maria_maggiore HTTP/1.1" 404 437 "-" "gobuster/3.0.1"
webserver.log:10.10.249.191 - - [18/Nov/2022:12:35:18 +0000] "GET /santa-clara-county HTTP/1.1" 404 437 "-" "gobuster/3.0.1"
webserver.log:10.10.249.191 - - [18/Nov/2022:12:35:20 +0000] "GET /texas-santa-barbara HTTP/1.1" 404 437 "-" "gobuster/3.0.1"
webserver.log:10.10.249.191 - - [18/Nov/2022:12:35:21 +0000] "GET /topicsantafe HTTP/1.1" 404 437 "-" "gobuster/3.0.1"
webserver.log:10.10.249.191 - - [18/Nov/2022:12:35:22 +0000] "GET /jasonsantamaria HTTP/1.1" 404 437 "-" "gobuster/3.0.1"
webserver.log:10.10.249.191 - - [18/Nov/2022:12:35:27 +0000] "GET /carlossantana_75 HTTP/1.1" 404 437 "-" "gobuster/3.0.1"
```
