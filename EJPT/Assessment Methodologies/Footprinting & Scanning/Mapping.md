
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
			- does not finish the connection but it sends a reset to drop the connection 
	- Service enumeration ![[Pasted image 20230504124923.png]]
		- connection with a service - listen back for information from service (banner). 
		  
- Connection to UDP 
	- nmap -iL {ips} (file is called ips and contains all the ips)
	- nmap -iL {ips} -sV -0 ![[Pasted image 20230504130653.png]]
	- nmap -iL {ips} -sV -0 -sC 

Other tools: 
- Nmap automator 
- masscan 
- rustscan 
- autorecon 

#### NMAP host discovery 
![[Pasted image 20230504133208.png]]
- based on the above results: ![[Pasted image 20230504133319.png]] 
	- scan services and operation systems 



-------- Labs ------------------

##### Scan for server  1 

- 1. run nmap {ip} -p- check all the ports 
- 2. check what ports are open and use in following command with service enumeration: nmap {ip} -p {port1},{port2...} -sV 
![[Pasted image 20230504165901.png]]

##### Scan for server  2 

- 1. check ip: ip a and ping 
- 2. check ports 1 to 250:     nmap {ip} -p 1-250 
- 3. run aggressive scan on port that is open: nmap {ip} -p {port} -A 
- 4.  run a udp scan: nmap {ip} -p 1-250 -sU 
- 5. hone into the open udp ports: nmap {ip} -p {port1},{port2...} -sUV
- 6. nmap {ip} -p {port} -sUV --script=discovery 



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

_--lab --_

1.  Identify the port running a Bind DNS server.

      - 177/udp open          domain     ISC BIND 9.10.3-P4 (Ubuntu Linux)

1.  Identify the port running a TFTP server.

      - Antwoord: ftp> status

Connected to 192.148.39.3.

Mode: netascii Verbose: off Tracing: off

2.  Identify the port running the SNMP server.

      - Antwoord: 234/udp open          snmp       SNMPv1 server; net-snmp SNMPv3 server (public)

Uitwerking

![[Pasted image 20230506135135.png]]

PORT   STATE    SERVICE

22/tcp open     ssh

80/tcp filtered http

177/tcp open  xdmcp

Aggressive scan results

22/tcp open     ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)

80/tcp filtered http

Running: Linux 4.X|5.X

OS details: Linux 4.15 - 5.6

177/tcp open  domain  ISC BIND 9.10.3-P4 (Ubuntu Linux)

OS details: Linux 4.15 - 5.6

Network Distance: 1 hop

Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

root@INE:~# nmap 192.148.39.3 -p 177,134,234 -sUV

PORT    STATE         SERVICE    VERSION

134/udp open|filtered ingres-net

177/udp open          domain     ISC BIND 9.10.3-P4 (Ubuntu Linux)

234/udp open          snmp       SNMPv1 server; net-snmp SNMPv3 server (public)

MAC Address: 02:42:C0:94:27:03 (Unknown)

Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Server 3 ---

1. ping the target ip to check connection

2. run nmap: nmap {target ip} -T4 (speed)

3. nmap {target ip} -T4 -p- (this will scan all tcp ports)

4. nmap {target ip} -T4 -sU (scan all udp ports)

5. scan (aggressive) the open port: nmap {target ip} -T4 -sU -p {port} -A

![[Pasted image 20230506143413.png]]

key is stored in /opt/david_key

check loopback ip

root@attackdefense:~# nmap  192.185.204.3 -T4 -sU

161/udp open  snmp

root@attackdefense:~# nmap  192.185.204.3 -T4 -sU -p 161 -A

PORT    STATE  SERVICE VERSION

161/udp open  snmp    SNMPv1 server; net-snmp SNMPv3 server (public)

snmp-interfaces:

|   lo

|     IP address: 127.0.0.1  Netmask: 255.0.0.0

|     Type: softwareLoopback  Speed: 10 Mbps

|     Traffic stats: 9.46 Kb sent, 9.46 Kb received

|   eth0

|     MAC address: 02:42:c0:b9:cc:03 (Unknown)

|     Type: ethernetCsmacd  Speed: 4 Gbps

|_    Traffic stats: 3.67 Mb sent, 3.94 Mb received

| snmp-netstat:

|   TCP  127.0.0.1:22         0.0.0.0:0

|   TCP  127.0.0.1:22         127.0.0.1:54774

|   TCP  127.0.0.1:1313       0.0.0.0:0

|   TCP  127.0.0.1:54774      127.0.0.1:22

|   TCP  127.0.0.11:35217     0.0.0.0:0

|   UDP  0.0.0.0:161          *:*

|   UDP  0.0.0.0:41896        *:*

|_  UDP  127.0.0.11:44823    

![[Pasted image 20230506150133.png]]

![[Pasted image 20230506150143.png]]

            tftp wil host-name en port niet -p

            results of: tftp {ip} {port} shows it is there, that port is hostng the tftp server (Trivial File Transfer Protocol. _TFTP_ is een protocol dat gebruikt kan worden om bestanden over te brengen via netwerken en tussen computers.)

_--lab --_

1.  Identify the port running a Bind DNS server.

      - 177/udp open          domain     ISC BIND 9.10.3-P4 (Ubuntu Linux)

1.  Identify the port running a TFTP server.

      - Antwoord: ftp> status

Connected to 192.148.39.3.

Mode: netascii Verbose: off Tracing: off

2.  Identify the port running the SNMP server.

      - Antwoord: 234/udp open          snmp       SNMPv1 server; net-snmp SNMPv3 server (public)

Uitwerking

![[Pasted image 20230506135135.png]]

PORT   STATE    SERVICE

22/tcp open     ssh

80/tcp filtered http

177/tcp open  xdmcp

Aggressive scan results

22/tcp open     ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)

80/tcp filtered http

Running: Linux 4.X|5.X

OS details: Linux 4.15 - 5.6

177/tcp open  domain  ISC BIND 9.10.3-P4 (Ubuntu Linux)

OS details: Linux 4.15 - 5.6

Network Distance: 1 hop

Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

root@INE:~# nmap 192.148.39.3 -p 177,134,234 -sUV

PORT    STATE         SERVICE    VERSION

134/udp open|filtered ingres-net

177/udp open          domain     ISC BIND 9.10.3-P4 (Ubuntu Linux)

234/udp open          snmp       SNMPv1 server; net-snmp SNMPv3 server (public)

MAC Address: 02:42:C0:94:27:03 (Unknown)

Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Server 3 ---

1. ping the target ip to check connection

2. run nmap: nmap {target ip} -T4 (speed)

3. nmap {target ip} -T4 -p- (this will scan all tcp ports)

4. nmap {target ip} -T4 -sU (scan all udp ports)

5. scan (aggressive) the open port: nmap {target ip} -T4 -sU -p {port} -A

![[Pasted image 20230506143413.png]]

key is stored in /opt/david_key

check loopback ip

root@attackdefense:~# nmap  192.185.204.3 -T4 -sU

161/udp open  snmp

root@attackdefense:~# nmap  192.185.204.3 -T4 -sU -p 161 -A

PORT    STATE  SERVICE VERSION

161/udp open  snmp    SNMPv1 server; net-snmp SNMPv3 server (public)

snmp-interfaces:

|   lo

|     IP address: 127.0.0.1  Netmask: 255.0.0.0

|     Type: softwareLoopback  Speed: 10 Mbps

|     Traffic stats: 9.46 Kb sent, 9.46 Kb received

|   eth0

|     MAC address: 02:42:c0:b9:cc:03 (Unknown)

|     Type: ethernetCsmacd  Speed: 4 Gbps

|_    Traffic stats: 3.67 Mb sent, 3.94 Mb received

| snmp-netstat:

|   TCP  127.0.0.1:22         0.0.0.0:0

|   TCP  127.0.0.1:22         127.0.0.1:54774

|   TCP  127.0.0.1:1313       0.0.0.0:0

|   TCP  127.0.0.1:54774      127.0.0.1:22

|   TCP  127.0.0.11:35217     0.0.0.0:0

|   UDP  0.0.0.0:161          *:*

|   UDP  0.0.0.0:41896        *:*

|_  UDP  127.0.0.11:44823    

![[Pasted image 20230506150133.png]]

![[Pasted image 20230506150143.png]]











