

The basics 

- **DNS** 
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


- **HTTP** 
	- URL ![[Pasted image 20230331111106.png]] 
		- **Scheme:** This instructs on what protocol to use for accessing the resource such as HTTP, HTTPS, FTP (File Transfer Protocol).  
		- **User:** Some services require authentication to log in, you can put a username and password into the URL to log in.  
		- **Host:** The domain name or IP address of the server you wish to access.  
		- **Port:** The Port that you are going to connect to, usually 80 for HTTP and 443 for HTTPS, but this can be hosted on any port between 1 - 65535.  
		- **Path:** The file name or location of the resource you are trying to access.  
		- **Query String:** Extra bits of information that can be sent to the requested path. For example, /blog?**id=1** would tell the blog path that you wish to receive the blog article with the id of 1.  
		- **Fragment:** This is a reference to a location on the actual page requested. This is commonly used for pages with long content and can have a certain part of the page directly linked to it, so it is viewable to the user as soon as they access the page.
		  
	- Making a request: 
		- ![[Pasted image 20230331111651.png]]
		- ![[Pasted image 20230331111821.png]]
		
	- Methods 
		- GET request    > getting information from web server 
		- POST request  > used for submitting data to the web server and potentially creating new records 
		- PUT request   > submitting data to web server to update information 
		- DELETE request     > used for deleting informaton/records from webserver 
		  
		  ![[Pasted image 20230331114923.png]]
		  
	- HTTP status code 
		- ![[Pasted image 20230331112332.png]]

	- Headers 
		- Common headers are: 
			- *host*  > specify which website you require 
			- *User agent* > your browser software and version number, this helps with correct formatting 
			- *content-length* > how much data to expect 
			- *accept-Encoding* > what type of compression method the browser supports so the data can be made smaller when transmitting 
			- *Cookie* > data sent to the server to help remember your information 
		- Common response headers 
			- *set-cookie*  > information to store which gets sent back to the web server on each request 
			- *cache-control* > how long to store the content of the response in the browser cache before it requests it again 
			-  *content-type*  > type of data returned 
			- *content-encoding*  > what method is used to compress the data 
			  
			  
	- Cookies 
		- Cookies are saved when you receive Set-Cookie header from web server - vanaf dan -- every request you make, you will send back cooki data to the web server 
		- HTTP is stateless (doest remember who you are)
		- Example HTTP request![[Pasted image 20230331113657.png]]

Basics: How websites work 
- ![[Pasted image 20230331115056.png]]
	- Front End  (Client-Side) > the way your browser renders a website  
	- Back End (Server side) > a server that processses your request and returns a response 
	