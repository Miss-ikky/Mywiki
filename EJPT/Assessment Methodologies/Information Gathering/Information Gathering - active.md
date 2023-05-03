
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
- 


