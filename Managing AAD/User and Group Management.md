*This section will look at the implementation of users and groups using the portal*

## Review Of Azure AD


### Key Challenges of using Cloud Services
- Managing Identity
- Provisioning and Deprovisioning accounts that can be connected to many different services
- Identifying what services are being used inside an organisation
- How do we manage Federations/ enterprise applications
- Reporting and Auditing 
- MAKE THE EXPERIENCE MORE PRODUCTIVE FOR THE USER 

### The Public Cloud
Services that are provided by a third party in a mult tenant environment that is accessed by the internet.

It is outside the enterprise environment 

### Authentication in the Cloud
*Communication with the public cloud has to be focused on protocols commonly available.*

HTTP and HTTPS are used with HTTPS preferred as it requires the that it is encrypted with SSL

### Authentication Commonly used 
 - OAUTH2 is used by AAD. It is primarily an Authorization protocol
 - OpenID Connect is an Authentication protocol that sits on top of OAUTH2 to provide Authentication Not Authorization
 - SAML/ Ws-Fed is is used with Federation 
 - Use **MFA** where possible.

### What is AAD
- A flat structure  allowing user and groups to be created
- Basic AD service features not the same as on windows server. It is geared towards the cloud
- No Kerberos on AAD as there is Internet communications
- Management is done through Portal and Powershell

## Utilizing Azure AD instances
## User and Group Management
## Manage User entitlements
