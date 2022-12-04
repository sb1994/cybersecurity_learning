IDOR, or Insecure Direct Object Reference, is the act of exploiting a misconfiguration in the way user input is handled, to access resources you wouldn't ordinarily be able to access. IDOR is a type of access control vulnerability.

For example, let's say we're logging into our bank account, and after correctly authenticating ourselves, we get taken to a URL like this [https://example.com/bank?account_number=1234](https://example.com/bank?account_number=1234). On that page we can see all our important bank details, and a user would do whatever they needed to do and move along their way thinking nothing is wrong.

There is however a potentially huge problem here, a hacker may be able to change the account_number parameter to something else like 1235, and if the site is incorrectly configured, then he would have access to someone else's bank information.

-----
### Details

ip =[10.10.238.237](http://10.10.238.237/)

- Logged in using the credentionals provided noot and test1234
- didnt find anything going up so went to 0 and found the flag![[Pasted image 20221204023923.png]]