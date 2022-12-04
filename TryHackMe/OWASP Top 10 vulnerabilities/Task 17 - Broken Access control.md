Websites have pages that are protected from regular visitors, for example only the site's admin user should be able to access a page to manage other users. If a website visitor is able to access the protected page/pages that they are not authorised to view, the access controls are broken.

  

A regular visitor being able to access protected pages, can lead to the following:

-   Being able to view sensitive information
-   Accessing unauthorized functionality

OWASP have a listed a few attack scenarios demonstrating access control weaknesses:  

  

Scenario #1: The application uses unverified data in a SQL call that is accessing account information:

pstmt.setString(1, request.getParameter("acct"));

ResultSet results = pstmt.executeQuery( );

  

An attacker simply modifies the ‘acct’ parameter in the browser to send whatever account number they want. If not properly verified, the attacker can access any user’s account.

http://example.com/app/accountInfo?acct=notmyacct

  

Scenario #2: An attacker simply force browses to target URLs. Admin rights are required for access to the admin page.

http://example.com/app/getappInfo

http://example.com/app/admin_getappInfo

  

If an unauthenticated user can access either page, it’s a flaw. If a non-admin can access the admin page, this is a flaw ([reference to scenarios](https://owasp.org/www-project-top-ten/OWASP_Top_Ten_2017/Top_10-2017_A5-Broken_Access_Control)).

  

To put simply, broken access control allows attackers to bypass authorization which can allow them to view sensitive data or perform tasks as if they were a privileged user.