### What is Email Analysis?

Email analysis is the process of extracting the email header information to expose the email file details. The email header contains the technical details of the email like sender, recipient, path, return address and attachments. Usually, these details are enough to determine if there is something suspicious/abnormal in the email and decide on further actions on the email, like filtering/quarantining or delivering. This process can be done manually and with the help of tools.

There are two main concerns in email analysis.

-   **Security issues:** Identifying suspicious/abnormal/malicious patterns in emails.
-   **Performance issues:** Identifying delivery and delay issues in emails.

In this task, we will focus on security concerns on emails, a.k.a. phishing. Before focusing on the hands-on email analysis, you will need to be familiar with the terms "social engineering" and "phishing".

-   **Social engineering:** Social engineering is the psychological manipulation of people into performing or divulging information by exploiting weaknesses in human nature. These "weaknesses" can be curiosity, jealousy, greed, kindness, and willingness to help someone.
-   **Phishing:** Phishing is a sub-section of social engineering delivered through email to trick someone into either revealing personal information and credentials or executing malicious code on their computer.

Phishing emails will usually appear to come from a trusted source, whether that's a person or a business. They include content that tries to tempt or trick people into downloading software, opening attachments, or following links to a bogus website. You can find more information on phishing by completing the [**phishing module**](https://tryhackme.com/module/phishing).

### Does the Email Analysis Still Matter?

Yes! Various academic research and technical reports highlight that phishing attacks are still extremely common, effective and difficult to detect. It is also part of penetration testing and red teaming implementations (paid security assessments that examine organisational cybersecurity). Therefore, email analysis competency is still an important skill to have. Today, various tools and technologies ease and speed up email analysis. Still, a skilled analyst should know how to conduct a manual analysis when there is no budget for automated solutions. It is also a good skill for individuals and non-security/IT people!

Important Note: In-depth analysis requires an isolated environment to work. It is only suggested to download and upload the received emails and attachments if you are in the authorised team and have an isolated environment. Suppose you are outside the corresponding team or a regular user. In that case, you can evaluate the email header using the raw format and conduct the essential checks like the sender, recipient, spam score and server information. Remember that you have to inform the corresponding team afterwards.

How to Analyse Emails?

Before learning how to conduct an email analysis, you need to know the structure of an email header. Let's quickly review the email header structure.

|**Field**|**Details**|
|---|----|
|**From**|The sender's address.|
|**To**|The receiver's address, including CC and BCC.
|**Date**|Timestamp, when the email was **sent.**
|**Subject**|The subject of the email.
|**Return Path**|- The return address of the reply, a.k.a. "Reply-To". If you reply to an email, the reply will go to the address mentioned in this field.
|**Domain Key and DKIM Signatures**|Email signatures are provided by email services to identify and authenticate emails.
|**SPF**|Shows the server that was used to send the email.t will help to understand if the actual server is used to send the email from a specific domain.
|**Message-ID**|Unique ID of the email.|
|**MIME-Version**|Used MIME version.It will help to understand the delivered "non-text" contents and attachments.
|**X-Headers**|The receiver mail providers usually add these fields. Provided info is usually experimental and can be different according to the mail provider.
|**X-Received**|Mail servers that the email went through.
|**X-Spam Status**|Spam score of the email.
|**X-Mailer**|Email client name.

---
Ran emlAnalyzier to extract data from the email
```
emlAnalyzer -i Urgent\:.eml --header --html -u --text --extract-all

==============
 ||  Header  ||
 ==============
X-Pm-Content-Encryption.....end-to-end
X-Pm-Origin.................internal
Subject.....................Urgent: Blue section is down. Switch to the load share plan!
From........................Chief Elf <chief.elf@santaclaus.thm>
Date........................Tue, 6 Dec 2022 00:00:01 +0000
Mime-Version................1.0
Content-Type................multipart/mixed;boundary=---------------------03edd9c682a0c8f60d54b9e4bb86659f
To..........................elves.all@santaclaus.thm <elves.all@santaclaus.thm>
X-Attached..................Division_of_labour-Load_share_plan.doc
Message-Id..................<QW9DMjAyMl9FbWFpbF9BbmFseXNpcw==>
X-Pm-Spamscore..............3
Received....................from mail.santaclaus.thm by mail.santaclaus.thm; Tue, 6 Dec 2022 00:00:01 +0000
X-Original-To...............elves.all@santaclaus.thm
Return-Path.................<murphy.evident@bandityeti.thm>
Delivered-To................elves.all@santaclaus.thm

 =========================
 ||  URLs in HTML part  ||
 =========================
[+] No URLs found in the html

 =================
 ||  Plaintext  ||
 =================
[+] Email contains no plaintext

 ============
 ||  HTML  ||
 ============
<span>Dear Elves,</span><div><br></div><div><span>Due to technical problems in the blue section of our toy factory, we are having difficulties preparing some toys. </span></div><div><br></div><div><span>There
 are a few days left to Christmas, so we need to use time efficiently to
 prepare every wishlist we receive. Due to that, the blue section's 
workload is shared with the rest to avoid any toy production delay.</span></div><div><br></div><div><span>The detailed division of labour is included in the attached document.</span></div><div><br></div><div><span>Good luck to you all.</span></div><div><br></div><div><b><span>Chief Elf</span></b></div><div><br></div>

 =============================
 ||  Attachment Extracting  ||
 =============================
[+] Attachment [1] "Division_of_labour-Load_share_plan.doc" extracted to eml_attachments/Division_of_labour-Load_share_plan.doc

```

Get the checksome using the following command:
```shell
sha256sum Division_of_labour-Load_share_plan.doc 

0827bb9a2e7c0628b82256759f0f888ca1abd6a2d903acdb8e44aca6a1a03467

```

checked the file score using the hash that was generated: using the url
https://www.virustotal.com/gui/file/0827bb9a2e7c0628b82256759f0f888ca1abd6a2d903acdb8e44aca6a1a03467

![[Pasted image 20221209210906.png]]

found that it uses different techniques https://vtbehaviour.commondatastorage.googleapis.com/0827bb9a2e7c0628b82256759f0f888ca1abd6a2d903acdb8e44aca6a1a03467_Zenbox.html?GoogleAccessId=758681729565-rc7fgq07icj8c9dm2gi34a4cckv235v1@developer.gserviceaccount.com&Expires=1670620892&Signature=YsYi5PhRGXXxg1QL9fWsT7aNNBiqHTklax%2BfFr1LvE9BvKXBLsWjWUcILXsROulT5qBbKWTqE%2FuP%0ANDpnoL9TL1sHzQJXlCAmgbBjFPD0m%2F0OzfEYPTQW16pjOpeu1Cj1MvTwQjIezogR96Ad%2Ftr1qbyN%0AVDbanLN%2Brgc%2B3HHmUj5y58jOXqFMr2rixJ%2BzhrT3ceb2BrfhQBHB8UQAlXQgCFoqMPCFhr2ZRaol%0A0DKNYVbAiQR7565vxhKs5FQ5Q1W6rmoyob75Q86rR3rnMJwTwBKrBkzP86fzoZaVhPgguhduY3vK%0AGfPzgpC4ciTPsQ8TlQZYg%2F8efNgp%2FKo4JQaKUg%3D%3D&response-content-type=text%2Fhtml;#mitre-attack

#### Mitre Attack

##### Execution
-   [T1064](https://attack.mitre.org/techniques/T1064/) Scripting confidence: high
##### Defense Evasion
-   [T1036](https://attack.mitre.org/techniques/T1036/) Masquerading confidence: low
-   [T1064](https://attack.mitre.org/techniques/T1064/) Scripting confidence: high
##### Discovery
-   [T1083](https://attack.mitre.org/techniques/T1083/) File and Directory Discovery confidence: low
-   [T1082](https://attack.mitre.org/techniques/T1082/) System Information Discovery confidence: low

Found that on inquest labs it cas a subcategory of [macro_hunter]( https://labs.inquest.net/dfi/sha256/0827bb9a2e7c0628b82256759f0f888ca1abd6a2d903acdb8e44aca6a1a03467).

found that the murphy.evident@bandityeti.thm is risky using https://emailrep.io/


can use the following code in the CMD to check programmatically 

```shell
curl emailrep.io/murphy.evident@bandityeti.thm

"email": "murphy.evident@bandityeti.thm",
    "reputation": "none",
    "suspicious": true,
    "references": 0,
    "details": {
        "blacklisted": false,
        "malicious_activity": false,
        "malicious_activity_recent": false,
        "credentials_leaked": false,
        "credentials_leaked_recent": false,
        "data_breach": false,
        "first_seen": "never",
        "last_seen": "never",
        "domain_exists": true,
        "domain_reputation": "low",
        "new_domain": false,
        "days_since_domain_creation": null,
        "suspicious_tld": false,
        "spam": false,
        "free_provider": false,
        "disposable": false,
        "deliverable": false,
        "accept_all": false,
        "valid_mx": false,
        "primary_mx": "",
        "spoofable": true,
        "spf_strict": false,
        "dmarc_enforced": false,
        "profiles": []
    }
```

Went to https://gchq.github.io/CyberChef/ to decode the Message-id found that it is the Base64 format https://gchq.github.io/CyberChef/#recipe=From_Base64('A-Za-z0-9%2B/%3D',true,false)&input=UVc5RE1qQXlNbDlGYldGcGJGOUJibUZzZVhOcGN3PT0

![[Pasted image 20221209220341.png]]
