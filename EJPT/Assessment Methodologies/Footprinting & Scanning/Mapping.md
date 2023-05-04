
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
	- Wireshark 
	- ARP-SCAN 
		- level 2 so we need sudo 
		- sudo arp-scan -i tap0 -g {ip/}
		![[Pasted image 20230504104639.png]]

	- Ping ![[Pasted image 20230504105749.png]]


- Fping 
  - fping -I tap0 -g {ip} -a (all the host that are alive)
  - When to many errors: fping -I tap0 -g {ip} -a *2>/dev/null* 
	  - Std in is 0 std out is 1 and std error is 2 , we willen de errors weghebben![[Pasted image 20230504110010.png]] 
  
  
  
- Nmap 
  
  
  
  
  
- ZenMap 
