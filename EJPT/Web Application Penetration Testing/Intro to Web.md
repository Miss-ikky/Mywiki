
- Websites: files on a server (hosted on a webserver or hosting)
- Webserver: pull files using HTTP. 
	- Webserver such as Apache, NGINX, ISS, Tomcat 
	- Off-premise Hosting such as AWS, Wordpress, DigitalOcean, Cloudflare. 


HTTP Procotol 
![[Pasted image 20230926114017.png]]

![[Pasted image 20230926114215.png]]

![[Pasted image 20230926114525.png]]

Browsers interpret and process the files on the webservers and shows it to the user. 

Sessions (HTTP does not maintain sessions but it uses cookies): ![[Pasted image 20230926114643.png]]
	Can be used for session hijacking 

login.php is file that is used by apache webserver 

First info gathering: 
- `nmap domainname `
- `nmap domainname -p port,port -sV `
  
inspect elements on webpage 
port 80 is http not https, if you spin up Wireshark you can listen to traffic bc its not encrypted. 

- `curl http://domain -v `

to find all the files stored on webserver: 
- ``dirb http://domainname``  (directory busting)


