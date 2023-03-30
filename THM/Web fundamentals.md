

The basics 

- DNS 
	- Domain Name System 
![[Pasted image 20230330143832.png]]
- Domain Hierarchy    --- example test.google.com 
	- TLD - Top Level Domain     --- e.g. .com 
		-  Two types of top level: 
			- gTLD (generic Top Level) which is used to tell the user the domains purpose e.g. .com .org & ccTLD (country Code Top Level Domain) says something about the geo  loation 
	- Second Level Domain  -- e.g. Google 
		- Limited to 63 characters + the TLD and can onyl use a-z 0-9 and hypens 
	- Subdomain   -- e.g. test.
		- A subdomain sists on the left-hand side of the Second Level Domain. Same restrictions as Second Level Doman. Max length is 253 characters 


	- Record types 
		- A record →  record resolve to IPv4 addresses 
		- AAAA record > record resolvse IPv6 address 
		- CNAMe > resolves to other domain name bijv een webshop van een website dienaar shopify.com gaat 
		- MX record > record resolves the address of the servers that handle the email for the domain querying for example an MX record response for tryhackme.com would look something like alt1.aspmx.l.google.com. These records also come with a priority flag. This tells the client in which order to try the servers, this is perfect for if the main server goes down and email needs to be sent to a backup server. 
		- TXT record >   free text fields where any text-based data can be stored.

	- Example: Making a DNS request 
		- ![[Pasted image 20230330151954.png]]
			1. Request a domain name > computer will check local cache > if not available in cache then request to you recursive DNS Server 
			2. Recursive DNS Server (provided by ISP) > looks locally in Cache > if not locally then request from Root DNS Server 
			3. Root DNS SServer (DNS Backbone of internet) redirect you to correct Top Level Domain Server. If you want to visit example.com then TLD is .com > refer you to TLD server that deals with .com addresses 
			4. TDL server stores records about authoritative server to answer DNS request, is also known as name server e.g. [uma.ns.cloudflare.com](http://uma.ns.cloudflare.com).  
			5. Authorative DNS server is the server that is responsible for storing the DNS records for particular domain naem and where updates to your domain anem DNS records would be made. >  Authorative DNS Server sends back DNS record to Recursive DNS Server. DNS records come with a TTL (= number represented in seconds that the response should be saved for locally until you have to look it up again )

- Usefull commands 

| command                         | uitleg |
| ------------------------------- | ------ |
| nsloopup *website*              |        |
| nsloopup --type=CNAME *website* |        |
| nsloopup --type= TXT  *website*           |        |
| nsloopup --type=MX     *website*         |        |
| nsloopup --type=A     *website*                           |        |
