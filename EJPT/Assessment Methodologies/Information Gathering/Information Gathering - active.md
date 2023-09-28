
## Active information Gathering 



#### DNS Zone Transfer 

- DNS servers by companes like Cloudflare (1.1.1.1) or Google (8.8.8.8) contain almost all domains on the internet ![[Pasted image 20230502145518.png]]
- Why DNS zone transfer 
	- DNS server admisn may want to copy or transfer zone files from one DNS server to another = zone transfer 
	- if misconfigured or unsecure, functionality can be abused by attackers to copy the zone file from the primary DNS server to another  DNS server 
	- A DNS Zone Transfer can provide pentesters holistic view of an organization network layout 


- DNS Zone Transfer 
	- use hostfile when you want to map specific domain to an ip  
	- `*dnsrecond* -d zonetransfer.me ` gives information about DNS records, je kan ook dnsdumpster.com gebruiken 
	- `*dnsenum* `can enumerate public available dns records 
	- `whatis dig`
	- `fierce -h  `
	- `fierce -dns  zonetransfer.me `

####  Host discovery with Nmap

- `sudo nmap *-sn* {ip} `
- `sudo apt-get install netdiscover -y `
- `sudo netdiscover -i eth0 -r {ip/x}   `
	- Netdiscover uses arp requests 


#### Port scanning with Nmap

TCP scan 
- default nmap scan:  *`nmap* {ip}    `
	-  Windows systems will block icmp pings, avoid ping `(-Pn)` 
	- `nmap -Pn {ip} ` :   niet checken if host is online just check the port 
- scan over all tcp port ranges met -p-, meer dan 60k ports : `nmap -Pn -p- {ip} `
	- specific range:` nmap -Pn -p1-1000 {ip}`
- scan specific defined port : `nmap -Pn -p 80 {ip} `
- scan most commonly used ports with -F: `nmap -Pn -F {ip} 
`
UDP 
- `nmap -Pn -sU {IP} `

`nmap -Pn -F -sV -0 -sC {ip}`
- aggrassive scan combines -sV (service detection scan) and -sC default script scan 

nmap timing template: 
- the lower the number the slower the scan ![[Pasted image 20230503121903.png]]![[Pasted image 20230503121957.png]]

save ouput  
- nmap gives you **3** output format options 
- `-oN  voor .txt file` 
- `-oX  voor .xml file `

steps
1. ping the target:  `ping -c 5 {ip} `
2. simple nmap 
3.   `nmap -Pn {ip}`
	-  `-Pn` (Treat all hosts as online -- skip host discovery) this  option will force the scanning even if it has detected the target as down in host discovery
4. run nmap on closed port to see the output: `nmap -Pn -p 443 {ip}`
    we have received port state as a filtered because target is up with a firewall ![[Pasted image 20230503131521.png]] 
	1. We will scan any random port which isn’t open. In this case scan port 443. If the port is not open we would receive “filtered” as an output to that port
6. if we want to discover the running application on port 80 we could use option  
`-sV` and this option is used to determine the application version information: `nmap -Pn -sV -p 80 {ip}`



