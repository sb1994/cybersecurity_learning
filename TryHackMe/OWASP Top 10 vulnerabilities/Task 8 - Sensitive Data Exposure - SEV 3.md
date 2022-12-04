
When a webapp accidentally divulges sensitive data, we refer to it as "Sensitive Data Exposure". This is often data directly linked to customers (e.g. names, dates-of-birth, financial information, etc), but could also be more technical information, such as usernames and passwords. At more complex levels this often involves techniques such as a "Man in The Middle Attack", whereby the attacker would force user connections through a device which they control, then take advantage of weak encryption on any transmitted data to gain access to the intercepted information (if the data is even encrypted in the first place...). Of course, many examples are much simpler, and vulnerabilities can be found in web apps which can be exploited without any advanced networking knowledge. Indeed, in some cases, the sensitive data can be found directly on the webserver itself.

--- 
The most common way to store a large amount of data in a format that is easily accessible from many locations at once is in a database. This is obviously perfect for something like a web application, as there may be many users interacting with the website at any one time. Database engines usually follow the Structured Query Language (SQL) syntax; however, alternative formats (such as NoSQL) are rising in popularity.

In a production environment it is common to see databases set up on dedicated servers, running a database service such as MySQL or MariaDB; however, databases can also be stored as files. These databases are referred to as "flat-file" databases, as they are stored as a single file on the computer. This is much easier than setting up a full database server, and so could potentially be seen in smaller web applications. Accessing a database server is outwith the scope of today's task, so let's focus instead on flat-file databases.

As mentioned previously, flat-file databases are stored as a file on the disk of a computer. Usually this would not be a problem for a webapp, but what happens if the database is stored underneath the root directory of the website (i.e. one of the files that a user connecting to the website is able to access)? Well, we can download it and query it on our own machine, with full access to everything in the database. Sensitive Data Exposure indeed!

---
The most common (and simplest) format of flat-file database is an _sqlite_ database. These can be interacted with in most programming languages, and have a dedicated client for querying them on the command line. This client is called "_sqlite3_", and is installed by default on Kali.

Let's suppose we have successfully managed to download a database:

![](https://i.imgur.com/tmRhcRE.png)

We can see that there is an SQlite database in the current folder.

To access it we use: `sqlite3 <database-name>`:

![](https://i.imgur.com/KJHAdI3.png)

From here we can see the tables in the database by using the `.tables` command:

![](https://i.imgur.com/kyIWl1q.png)

At this point we can dump all of the data from the table, but we won't necessarily know what each column means unless we look at the table information. First let's use `PRAGMA table_info(customers);` to see the table information, then we'll use `SELECT * FROM customers;` to dump the information from the table:

![](https://i.imgur.com/wVvHk7a.png)

We can see from the table information that there are four columns: custID, custName, creditCard and password. You may notice that this matches up with the results. Take the first row:

`0|Joy Paulson|4916 9012 2231 7905|5f4dcc3b5aa765d61d8327deb882cf99   `   

We have the custID (0), the custName (Joy Paulson), the creditCard (4916 9012 2231 7905) and a password hash (5f4dcc3b5aa765d61d8327deb882cf99).

--- 
In the previous task we saw how to query an SQLite database for sensitive data. We found a collection of password hashes, one for each user. In this task we will briefly cover how to crack these.

When it comes to hash cracking, Kali comes pre-installed with various tools -- if you know how to use these then feel free to do so; however, they are outwith the scope of this material.

Instead we will be using the online tool: [Crackstation](https://crackstation.net/). This website is extremely good at cracking weak password hashes. For more complicated hashes we would need more sophisticated tools; however, all of the crackable password hashes used in today's challenge are weak MD5 hashes, which Crackstation should handle very nicely indeed.

When we navigate to the website we are met with the following interface:

![](https://i.imgur.com/oioleck.png)

Let's try pasting in the password hash for Joy Paulson which we found in the previous task (`5f4dcc3b5aa765d61d8327deb882cf99`). We solve the Captcha, then click the "Crack Hashes" button:

![](https://i.imgur.com/KwHMGGV.png)

We see that the hash was successfully broken, and that the user's password was "password" -- how secure!

It's worth noting that Crackstation works using a massive wordlist. If the password is not in the wordlist then Crackstation will not be able to break the hash.  

The challenge is guided, so if Crackstation fails to break a hash in today's box you can assume that the hash has been specifically designed to not be crackable.

---   

## Details

1. Access the site and check out the source code in the browser
Looked at the app using 10.10.18.59 in the browser. found a note in the /login page mentioning the /assets. Could contains DB credentionals
```html 
</header>
		<div class=background></div>
		<!-- Must remember to do something better with the database than store it in /assets... -->
</header>
```
- Also found that the post form leads to a /api/login route and that the inputs are user name and password
```html 
<form method="POST" action="/api/login">
	<input type="text" name="username" placeholder="Username"><br>
	<input type="password" name="password" placeholder="Password"><br>
	<input id="loginBtnFunc" type="submit" value="Login!">
</form>
```

- Found the webapp.db which is Sqlite3  so I dowloaded it and opened it. Found that it has 2 tables
```shell
sqlite3 webapp.db 

SQLite version 3.39.2 2022-07-21 15:24:47
Enter ".help" for usage hints.

sqlite> .tables
sessions  users   
```
- Did a select all on the users and found the usernames Bob, Admin, Alice and their password hashs, found that they have a 1 or 0 (could be site role?)
```sqlite



select * from users;

4413096d9c933359b898b6202288a650|admin|6eea9b7ef19179a06954edd0f6c05ceb|1
23023b67a32488588db1e28579ced7ec|Bob|ad0234829205b9033196ba818f7a872b|1
4e8423b514eef575394ff78caed3254d|Alice|268b38ca7b84f44fa0a6cdc86e6301e0|0

```
- Added 6eea9b7ef19179a06954edd0f6c05ceb to the Crackstation and got the password for the admin  - qwertyuiop
![[Pasted image 20221203235341.png]]
- Logged in using the admin username and password and got the flag![[Pasted image 20221203235614.png]]
- 