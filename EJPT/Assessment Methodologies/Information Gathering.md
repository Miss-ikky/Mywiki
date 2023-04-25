-  First step of any penetration test  involves gathering information
-  Information gathering 2 types 
	- Passive infromation gathering : involves gathering as much info as possible **wihout** actively engaging with the target - je gebruikt wat publiekelijk available is. 
	- Active information gathering : involves gathering as much info as possible by actively engaging with the target system (autorizatie nodig van bedrijf)

![[Pasted image 20230424153214.png]]

# Passive information Gathering 


#### Website recon & Footprinting 

-  Look for the following information: 
	-  Ip addresses 
	- Directores hidden from search engine -> robot.txt 
	- Names, Email address, phone number, fysieke address, web tech being used 
- Demo 
	- Producten site: IP address is often hidden behind proxy/fireall (e.g. cloudflare)
		-  resolve domain name to ip address:  *host* {domain name}
		 ![[Pasted image 20230425102124.png]]
			 2 ips: 1 ervan is van proxy/firewall 
	
	-  Webcrawler scours website to index it to search engine. 
		- Robot.txt allows you to specify what folders and files you  dont want search enginges to index. (Allow/Disallow)
			- /wp-admin/ = wordpress 
		
	- Sitemap.xml - file that allows search engine an organized way of indexing 
		- Interesting to look at if you are pentesting a Wordpress website, look into the **categories** that are hidden on the front-end
		   
	- Interesting resourcse: 
		- Browser plugin: 
			- mozilla add-on: *built with* tells you what technologies is running on that website
			- mozilla add-on: *wappalyzer*  web technology profiler 
			
	- WhatWeb (built in Kali)
		-  *whatweb* {domain}![[Pasted image 20230425104736.png]]
		
		- Download the entire website to analyze source code: httrack 
			- sudo apt-get install webhttrack 

#### Whois Enumeration 

-  *who is* {domain}  ![[Pasted image 20230425153648.png]]
	- DNSSEC: Enabled - personal info is all redacted for privacy reasons  ![[Pasted image 20230425153911.png]]
- who.is website for more info / can identify nameservers of particular domain 
- *host* {domain}
   ![[Pasted image 20230425154304.png]]

#### Website Footprinting With Netcraft 
- Netcraft:  used to gather information about target domian (services menu - data mining) ![[Pasted image 20230425165716.png]]
