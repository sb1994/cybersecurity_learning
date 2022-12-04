
Ah yes, the origin of many memes. Cookies are an essential tool for modern websites to function. Tiny pieces of data, these are created by a website and stored on the user's computer. 

![](https://i.imgur.com/phg51EI.png)

You'll see notifications like the above on most websites these days. Websites use these cookies to store user-specific behaviours like items in their shopping cart or session IDs.

In the web application, we're going to exploit, you'll notice cookies store login information like the below! Yikes!

  

![](https://i.imgur.com/QhR7aOX.png)

  

Whilst plaintext credentials is a vulnerability in itself, it is not insecure deserialization as we have not sent any serialized data to be executed!

Cookies are not permanent storage solutions like databases. Some cookies such as session ID's will clear when the browser is closed, others, however, last considerably longer. This is determined by the "Expiry" timer that is set when the cookie is created.

_Some cookies have additional attributes, a small list of these are below:_

|Attribute | Description| Required?| 
|------------ |------------ | ------------| 
|Cookie Name | The Name of the Cookie to be set|Yes| 
|Cookie Value | Value, this can be anything plaintext or encoded|Yes|
|Secure Only | If set, this cookie will only be set over HTTPS connections|No|
|Expiry | Set a timestamp where the cookie will be removed from the browser|No|
|Path | The cookie will only be sent if the specified URL is within the request|No|

Cookies are transfered over HTTPS

----
Created the user and then decode the session ID cookies 
![[Pasted image 20221204135726.png]]
which is in base62

