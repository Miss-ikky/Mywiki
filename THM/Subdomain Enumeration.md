
Subdomain enumeration is the process of finding valid subdomains for a domain, but why do we do this? We do this to expand our attack surface to try and discover more potential points of vulnerability 


**Methods**
- OSINT 
	-  SSL/TLS Certificates: When an SSL/TLS (Secure Sockets Layer/Transport Layer Securit. 
		- certificate is created for a domain by a CA (Certificate Authority), CA's take part in what's called "Certificate Transparency (CT) logs". These are publicly accessible logs of every SSL/TLS certificate created for a domain name. The purpose of Certificate Transparency logs is to stop malicious and accidentally made certificates from being used. We can use this service to our advantage to discover subdomains belonging to a domain, sites like [https://crt.sh](http://crt.sh) and [https://ui.ctsearch.entrust.com/ui/ctsearchui](https://ui.ctsearch.entrust.com/ui/ctsearchui) offer a searchable database of certificates that shows current and historical results. 
	- search engine 
		- use search query in gogole such as -site:{domain} site: \*.{domain}
	- Automation Using Sublist3r
		- To speed up the process of OSINT subdomain discovery, we can automate the above methods with the help of tools like [Sublist3r](https://github.com/aboul3la/Sublist3r): ![[Pasted image 20230406140651.png]]
		  
		  
- Bruteforce
	- Bruteforce DNS (Domain Name System) enumeration is the method of trying different possible subdomains from a pre-defined list of commonly used subdomains. ![[Pasted image 20230406140459.png]]
- Virtual Hosts 