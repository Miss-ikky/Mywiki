
## Active information Gathering 



#### DNS Zone Transfer 

- DNS servers by companes like Cloudflare (1.1.1.1) or Google (8.8.8.8) contain almost all domains on the internet ![[Pasted image 20230502145518.png]]
- Why DNS zone transfer 
	- DNS server admisn may want to copy or transfer zone files from one DNS server to another = zone transfer 
	- if misconfigured or unsecure, functionality can be abused by attackers to copy the zone file from the primary DNS server to another  DNS server 
	- A DNS Zone Transfer can provide pentesters holistic view of an organization network layout 


- DNS Zone Transfer 
	- use hostfile when you want to map specific domain to an ip  
	- *dnsrecond* -d zonetransfer.me  gives information about DNS records, je kan ook dnsdumpster.com gebruiken 
	- *dnsenum* can enumerate public available dns recorsd ![[Pasted image 20230502151251.png]]
	- *dig* ![[Pasted image 20230502152035.png]]
	- *fierce* -h  
	- *fierce* -dns  zonetransfer.me ![[Pasted image 20230502152403.png]]

####  Host discovery with Nmap

- sudo nmap *-sn* {ip} 
- sudo apt-get install netdiscover -y 
- sudo netdiscover -i eth0 -r {ip/x}   
	- Netdiscover uses arp requests 


#### Port scanning with Nmap

TCP scan 
- default nmap scan:  *nmap* {ip}    
	-  Windows systems will block icmp pings, avoid ping (-Pn) 
	- nmap -Pn {ip}  :   niet checken if host is online just check the port 
- scan over all tcp port ranges met -p-, meer dan 60k ports : nmap -Pn -p- {ip} 
	- specfieke range: nmap -Pn -p1-1000 {ip}   will scan port  1 to 1000 
- scan specific defined port : nmap -Pn -p 80 {ip} 
- scan most commonly used ports with -F: nmap -Pn -F {ip} 

UDP 
- default nmap is tcp scan ; enter to see status ; verbosity output  met -v 
- nmap -Pn *-sU* {IP} 


More
![[Pasted image 20230503115859.png]]
- *-sV* : service version detection scan on open ports 

- default nmap script scan: *-sC*   will run list of nmap scripts that are open to enumerate more information about open ports -> ****nmap -Pn -F -sV -0 -sC {ip}
- aggrassive scan combines -sV (service detection scan) and -sC default script scan 
![[Pasted image 20230503121712.png]]

nmap timing template: 
- the lower the number the slower the scan ![[Pasted image 20230503121903.png]]![[Pasted image 20230503121957.png]]

save ouput  
- nmap gives you 3 output format options 
- *-oN*  voor .txt file 
- *-oX*  voor .xml file 


--- Lab --- 

Task: The target machine is running a Windows Firewall.  Your task is to discover available live hosts and their open ports using Nmap and identify the running services and applications. 

instructions:  
- Your Kali machine has an interface with IP address 10.10.X.Y. Run “ip addr” to know the values of X and Y.
-  The IP address of the target machine is mentioned in the file “/root/Desktop/target”
-  Do not attack the gateway located at IP address 192.V.W.1 and 10.10.X.1 

Solution: 

*root@attackdefense:~# ip addr*
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: ip_vti0@NONE: <NOARP> mtu 1480 qdisc noop state DOWN group default qlen 1000
    link/ipip 0.0.0.0 brd 0.0.0.0
202290: eth0@if202291: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:0a:01:00:0d brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.1.0.13/16 brd 10.1.255.255 scope global eth0
       valid_lft forever preferred_lft forever
202292: eth1@if202293: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:0a:0a:18:03 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.10.24.3/24 brd 10.10.24.255 scope global eth1
       valid_lft forever preferred_lft forever


*root@attackdefense:~# sudo nmap -Pn -F 10.2.16.33*
sudo: setrlimit(RLIMIT_CORE): Operation not permitted
Starting Nmap 7.70 ( https://nmap.org ) at 2023-05-03 16:32 IST
Nmap scan report for 10.2.16.33
Host is up (0.0031s latency).
Not shown: 93 filtered ports
PORT      STATE SERVICE
80/tcp    open  http
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
3389/tcp  open  ms-wbt-server
49154/tcp open  unknown
49155/tcp open  unknown


root@attackdefense:~# nmap -Pn -F -sV -v 10.2.16.33

Starting Nmap 7.70 ( https://nmap.org ) at 2023-05-03 16:36 IST
NSE: Loaded 43 scripts for scanning.
Initiating Parallel DNS resolution of 1 host. at 16:36
Completed Parallel DNS resolution of 1 host. at 16:36, 0.00s elapsed
Initiating SYN Stealth Scan at 16:36
Scanning 10.2.16.33 [100 ports]
Discovered open port 80/tcp on 10.2.16.33
Discovered open port 135/tcp on 10.2.16.33
Discovered open port 3389/tcp on 10.2.16.33
Discovered open port 139/tcp on 10.2.16.33
Discovered open port 445/tcp on 10.2.16.33
Discovered open port 49154/tcp on 10.2.16.33
Discovered open port 49155/tcp on 10.2.16.33
Completed SYN Stealth Scan at 16:36, 1.87s elapsed (100 total ports)
Initiating Service scan at 16:36
Scanning 7 services on 10.2.16.33
Nmap scan report for 10.2.16.33
Host is up (0.0031s latency).
Not shown: 93 filtered ports
PORT      STATE SERVICE            VERSION
80/tcp    open  http               HttpFileServer httpd 2.3
135/tcp   open  msrpc              Microsoft Windows RPC
139/tcp   open  netbios-ssn        Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds       Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
3389/tcp  open  ssl/ms-wbt-server?
49154/tcp open  msrpc              Microsoft Windows RPC
49155/tcp open  msrpc              Microsoft Windows RPC
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 84.37 seconds
           Raw packets sent: 193 (8.492KB) | Rcvd: 7 (308B)



Answers: 
Name Windows Recon: Nmap Host Discovery  
URL https://attackdefense.com/challengedetails?cid=2219  
Type Windows Reconnaissance: SMB

1. ping the target:  ping -c 5 {ip} 
2. run a simple nmap scan on the target: nmap {ip} 
3. use -Pn option to force the nmap to scan ports even if the host is down: nmap -Pn {ip}
	1. -Pn (Treat all hosts as online -- skip host discovery) this  option will force the scanning even if it has detected the target as down in host discovery
4. run nmap on closed port to see the output: nmap -Pn -p 443 {ip}
5. we have received port state as a filtered because target is up with a firewall ![[Pasted image 20230503131521.png]] 
	1. We will scan any random port which isn’t open. In this case scan port 443. If the port is not open we would receive “filtered” as an output to that port
	2. About Filtered port:  “Nmap cannot determine whether the port is open because packet filtering prevents its probes  from reaching the port. The filtering could be from a dedicated firewall device, router rules, or  host-based firewall software. These ports frustrate attackers because they provide so little  information. Sometimes they respond with ICMP error messages such as type 3 code 13  destination unreachable: communication administratively prohibited), but filters that simply drop  probes without responding are far more common. This forces Nmap to retry several times just in  case the probe was dropped due to network congestion rather than filtering. This slows down the scan dramatically.
6. if we want to discover the running application on port 80 we could use option  
-sV and this option is used to determine the application version information: nmap -Pn -sV -p 80 {ip}
7. 


