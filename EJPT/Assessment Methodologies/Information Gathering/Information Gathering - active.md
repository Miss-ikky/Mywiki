
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


