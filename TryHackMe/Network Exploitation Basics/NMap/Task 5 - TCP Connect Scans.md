#scantypes  
## How TCP scans work 

TCP Connect scan works by performing the three-way handshake with each target port in turn. In other words, Nmap tries to connect to each specified TCP port, and determines whether the service is open by the response it receives.

if Nmap sends a TCP request with the _SYN_ flag set to a **_closed_** port, the target server will respond with a TCP packet with the _**RST**_ (Reset) flag set. By this response, Nmap can establish that the port is closed.

If, however, the request is sent to an _open_ port, the target will respond with a TCP packet with the SYN/ACK flags set. Nmap then marks this port as being _open_ (and completes the handshake by sending back a TCP packet with ACK set).

RFC 793 defines the appropriate behaviour for the TCP protocol

That said, it is very easy to configure a firewall to respond with a RST TCP packet. For example, in IPtables for Linux, a simple version of the command would be as follows:

```shell
iptables -I INPUT -p tcp --dport <port> -j REJECT --reject-with tcp-reset
```