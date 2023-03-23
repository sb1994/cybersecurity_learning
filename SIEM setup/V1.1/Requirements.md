
1. VirtualBox
2. One Attack machine and one Victim Machine
3. Have host only network for both so ssh can be used

## IPs
- Victim - 192.168.56.5
- Attacker - 192.168.56.3
## Services on Victim Machine
1. MySQL
	- Credentials - root : password123



### Initial Setup of the Lab

1. assign the NAT and Host Only adapters for the Victim  
```shell
enp0s3: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.0.2.15  netmask 255.255.255.0  broadcast 10.0.2.255
        inet6 fe80::a00:27ff:fe29:cf27  prefixlen 64  scopeid 0x20<link>
        ether 08:00:27:29:cf:27  txqueuelen 1000  (Ethernet)
        RX packets 176  bytes 19863 (19.3 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 220  bytes 21027 (20.5 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

enp0s8: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.56.4  netmask 255.255.255.0  broadcast 192.168.56.255
        inet6 fe80::a00:27ff:fe6b:342b  prefixlen 64  scopeid 0x20<link>
        ether 08:00:27:6b:34:2b  txqueuelen 1000  (Ethernet)
        RX packets 2379  bytes 163088 (159.2 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 230  bytes 29863 (29.1 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 18  bytes 2112 (2.0 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 18  bytes 2112 (2.0 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```
3. assign the NAT and Host Only adapters for the Attacker
```shell
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.0.2.15  netmask 255.255.255.0  broadcast 10.0.2.255
        inet6 fe80::d27e:95ad:7b16:d8be  prefixlen 64  scopeid 0x20<link>
        ether 08:00:27:b1:9d:67  txqueuelen 1000  (Ethernet)
        RX packets 26  bytes 2633 (2.5 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 68  bytes 7853 (7.6 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.56.3  netmask 255.255.255.0  broadcast 192.168.56.255
        inet6 fe80::ec97:c9b0:8c22:6ac  prefixlen 64  scopeid 0x20<link>
        ether 08:00:27:5f:09:7c  txqueuelen 1000  (Ethernet)
        RX packets 355  bytes 50633 (49.4 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 2211  bytes 155513 (151.8 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 4  bytes 240 (240.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 4  bytes 240 (240.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

## Setup Mysql
3. Update and install MySql on the Victim Machine
```shell
sudo yum update

sudo yum install mysql-server

```
4. Start the service
```shell
sudo systemctl start mysqld.service
```
5. Check the status of the service
```shell
● mysqld.service - MySQL 8.0 database server
     Loaded: loaded (/usr/lib/systemd/system/mysqld.service; disabled; preset: disabled)
     Active: active (running) since Tue 2023-01-31 02:16:26 GMT; 1min 33s ago
    Process: 94393 ExecStartPre=/usr/libexec/mysql-check-socket (code=exited, status=0/SUCCESS)
    Process: 94415 ExecStartPre=/usr/libexec/mysql-prepare-db-dir mysqld.service (code=exited, status>
   Main PID: 94496 (mysqld)
     Status: "Server is operational"
      Tasks: 38 (limit: 22992)
     Memory: 453.6M
        CPU: 10.402s
     CGroup: /system.slice/mysqld.service
             └─94496 /usr/libexec/mysqld --basedir=/usr
```
6. enable the service with every login
```shell
sudo systemctl enable mysqld

Created symlink /etc/systemd/system/multi-user.target.wants/mysqld.service → /usr/lib/systemd/system/mysqld.service.
```
7. Secure SQL installation
```shell
sudo mysql_secure_installation

Securing the MySQL server deployment.

Connecting to MySQL using a blank password.

VALIDATE PASSWORD COMPONENT can be used to test passwords
and improve security. It checks the strength of password
and allows the users to set only those passwords which are
secure enough. Would you like to setup VALIDATE PASSWORD component?

Press y|Y for Yes, any other key for No: n
Please set the password for root here.

New password:

Re-enter new password:
By default, a MySQL installation has an anonymous user,
allowing anyone to log into MySQL without having to have
a user account created for them. This is intended only for
testing, and to make the installation go a bit smoother.
You should remove them before moving into a production
environment.

Remove anonymous users? (Press y|Y for Yes, any other key for No) : y
Success.


Normally, root should only be allowed to connect from
'localhost'. This ensures that someone cannot guess at
the root password from the network.

Disallow root login remotely? (Press y|Y for Yes, any other key for No) : y
Success.

By default, MySQL comes with a database named 'test' that
anyone can access. This is also intended only for testing,
and should be removed before moving into a production
environment.


Remove test database and access to it? (Press y|Y for Yes, any other key for No) : y
 - Dropping test database...
Success.

 - Removing privileges on test database...
Success.

Reloading the privilege tables will ensure that all changes
made so far will take effect immediately.

Reload privilege tables now? (Press y|Y for Yes, any other key for No) : y
Success.
```

8. Login to the sql server
```shell

sudo mysqladmin -u root -p version

Enter password:
mysqladmin  Ver 8.0.30 for Linux on x86_64 (Source distribution)
Copyright (c) 2000, 2022, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Server version          8.0.30
Protocol version        10
Connection              Localhost via UNIX socket
UNIX socket             /var/lib/mysql/mysql.sock
Uptime:                 9 min 38 sec

Threads: 2  Questions: 13  Slow queries: 0  Opens: 133  Flush tables: 3  Open tables: 49  Queries per second avg: 0.022
```

9. Login to the root server
```shell
mysql -u root -p

Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 14
Server version: 8.0.30 Source distribution

Copyright (c) 2000, 2022, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

## Reconicessnance

1. Checked what the port is for mysql on the victim machine
```shell
mysql> SHOW GLOBAL VARIABLES LIKE 'PORT';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| port          | 3306  |
+---------------+-------+
1 row in set (0.01 sec)

mysql>
```

