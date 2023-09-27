
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
	- Ping - test connectivity to computer


- `Fping` 
  - `fping -I tap0 -g {ip} -a `(all the host that are alive)
  - When to many errors: `fping -I tap0 -g {ip} -a *2>/dev/null* `
	  - Std in is 0 std out is 1 and std error is 2 , we willen de errors weghebben![[]]   
		213 could be that it is not responding to messages, ARP did find the host and ping did not. Therefore use multiple tools 

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
			- does not finish the connection but it sends a reset to drop the connection 
	- Service enumeration ![[Pasted image 20230504124923.png]]
		- connection with a service - listen back for information from service (banner). 
		  
- Connection to UDP 
	- `nmap -iL {ips} `(file is called ips and contains all the ips)
	- `nmap -iL {ips} -sV -0 `
	- `nmap -iL {ips} -sV -0 -sC `

Other tools: 
- `Nmap automator `
- `masscan` 
- `rustscan` 
- `autorecon` 

#### NMAP host discovery 


- 1. check ip: `ip a and ping` 
- 2. check ports 1 to 250:     `nmap {ip} -p 1-250 `
- 3. run aggressive scan on port that is open: `nmap {ip} -p {port} -A `
- 4.  run a udp scan: `nmap {ip} -p 1-250 -sU `
- 5. hone into the open udp ports: `nmap {ip} -p {port1},{port2...} -sUV`
- 6. `nmap {ip} -p {port} -sUV --script=discovery `



Server 1

1. Ping the ip

2. nmap {target ip} -p 1-250

3. run aggresive scan on open port: nmap {target ip} -p {port} -A

      1. Aggressive scan will look for service, finger printing, operating system,  get the banner, trace route, and more

4. run same scan again but this time for UDP: nmap {target ip} -p 1-250 -*sU*

5. hone in the open ports with a service scan: nmap {target ip} -p {port1, port2.port3 etc} -sUV

      1. Filtered means there was no response. Closed means the connection was refused. When a port gives back filtered try run the discovery script:

            1. nmap {target ip} -p {port that gives filtered} -sUV --script=discovery

      2. if still no results check the connection: tftp {ip} -p {port}  

            1. tftp> *status*    if result is not connected try again

            2. tftp> quit

            3. tftp {ip} {port}

               Filtered means there was no response. Closed means the connection was refused.

            ![[Pasted image 20230506134243.png]] 

Server 2

1. Ping the ip

2. nmap {target ip} -p 1-250

3. run aggresive scan on open port: nmap {target ip} -p {port} -A

      1. Aggressive scan will look for service, finger printing, operating system,  get the banner, trace route, and more

4. run same scan again but this time for UDP: nmap {target ip} -p 1-250 -*sU*

5. hone in the open ports with a service scan: nmap {target ip} -p {port1, port2.port3 etc} -sUV

      1. Filtered means there was no response. Closed means the connection was refused. When a port gives back filtered try run the discovery script:

            1. nmap {target ip} -p {port that gives filtered} -sUV --script=discovery

      2. if still no results check the connection: tftp {ip} -p {port}  

            1. tftp> *status*    if result is not connected try again

            2. tftp> quit

            3. tftp {ip} {port}

               Filtered means there was no response. Closed means the connection was refused.

            ![[Pasted image 20230506134243.png]]

            tftp wil host-name en port niet -p

            results of: tftp {ip} {port} shows it is there, that port is hostng the tftp server (Trivial File Transfer Protocol. _TFTP_ is een protocol dat gebruikt kan worden om bestanden over te brengen via netwerken en tussen computers.)






