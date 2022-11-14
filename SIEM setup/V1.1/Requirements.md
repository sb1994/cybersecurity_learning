
1. VirtualBox
2. pfSense Firewall implemented in a VM
3. VM clients that connect through pfSense on a NAT network. 

## Host Machine Specs


## Step 1  - Download and install pfSense

1.  Download ISO image from https://www.pfsense.org/download/
2.  Create VM in VirtualBox 
	1. Need two NICs one for WAN(NAT)  and LAN(Internal Network) 
	2. NAT Network 
			- **Name**: InfoSec NAT
			- **Default Gateway**: 192.168.100.0/24 
3. Need to extract the ISO so use 7Zip as WinZip isnt free[]
4. Load the ISO  in the VM

5. Setup the IP for the WebUI : 
	1. Select Option 2  twice for the LAN option and set to 192.168.50.1

6. Client Kali first adaptor needs to be on the same internal network as the pfSense firewall![[Pasted image 20221113212937.png]]





## References

---------
Basic pfSense Setup
https://www.youtube.com/watch?v=WM4q5btBFl4&ab_channel=InfoSecPat

-------------
