
#### Mapping a network 


Process
- Pyshical access 
	- Pysical security 
	- OSINT 
	- Social Engineering 

- Sniffing 
	- Passive Reconnaissance 
	- Watch network traffic 

- ARP (Address resolution protocol)
	- Resolve IP to Mac address 
	- broadcast who has? tell .. 
  
- ICMP 
	- Traceroute 
	- Ping - Type 8 echo request 

- Tools 
	- Wireshark - capture network traffic and identify endpoints from that traffic 
	- ARP-SCAN 
		- level 2 so we need sudo 
		- sudo arp-scan -i tap0 -g {ip/}
		![[Pasted image 20230504104639.png]]

	- Ping - test connectivity to computer![[Pasted image 20230504105749.png]]


- Fping 
  - fping -I tap0 -g {ip} -a (all the host that are alive)
  - When to many errors: fping -I tap0 -g {ip} -a *2>/dev/null* 
	  - Std in is 0 std out is 1 and std error is 2 , we willen de errors weghebben![[Pasted image 20230504110010.png]]  ![[Pasted image 20230504114257.png]] 
		213 could be that it is not responding to messages, ARP did find the host and ping did not. Therefore use multiple tools 

- Nmap 
- ZenMap - gui version of nmap

##### Port scanning

- Identify operating systems 
	- Send request - examine response and compare to signature database 
	 ![[Pasted image 20230504115231.png]]
	- Scan for services by scanning for ports and their response 
		- Normal TCP connection where the port is open: ![[Pasted image 20230504115420.png]]
			- you can send a RST + ACK now that you know that the port is open to close the connection 
		- Connection to TCP port that is closed: ![[Pasted image 20230504115558.png]]
			- a reset indicates that the port is closed and its not possible to connect 
		- Connect to TCP - Stealthy ![[Pasted image 20230504115657.png]]
			- 
