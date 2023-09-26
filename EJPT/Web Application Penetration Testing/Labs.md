
##### Web and HTTP protocol 

- Use inspector to inspect logon page (method and where it is send)
- dirb for directory busting:
	- `dirb http://ip `
- Check what options are possible for the webfiles/directories found with dir: 
	- `curl -X GET ip `
	- `curl -I ip `
	- `curl -X OPTIONS ip -v`
	- `curl -X PUT ip `
	- `curl -X OPTIONS ip/loging.php -v `
	- `curl -X POST ip `
	- `curl -X POSt ip/login.php -d "name=john&password=password" -v` 
	- `curl -X OPTIONS ip/page_of_interest -v `

If PUT is possible: `curl ip/uploads/ --upload-file file.txt`


##### Directory enumeration with GoBuster 


- similar to dirb 
- `gobuster`  (manual)
- `gobuster dir -u http://ip -w /usr/share/wordlist/dirb/common.txt `
- `gobuster dir -u http://ip -w /usr/share/wordlist/dirb/common.txt -b 403,404 ` (filter out 403 and 404)





##### Directory Enumeration with Burp Suite 

- turn on proxyfox 
- Action - send to intruder 
- check target 
- intruder (positions)
- ` Get /$name$` ![[Pasted image 20230926152712.png]] 
	- Sniper (directly) Attack type are different 
- Payloads
	- Add: data, passwords, phphmyadmin, js, images, 
	- load wordlist from kali: wordlists -> dirb --> common


##### Scanning Web Application with ZAProxy 

- run nmap scan 
open owasp zap 
click around on website to collect data 
![[Pasted image 20230926180039.png]]

Select form based authentication
![[Pasted image 20230926180141.png]]
Make sure user mode is enabled 
Use the spider to do enumaration 

##### Scanning web Applications with Nikto 

- `ping` and `nmap` for port 80 
- `nikto` 
- `nikto -Help `
- `nikto -h http://ip `
- `nikto -h http://ip/full_url -Tuning 5 -Display V






##### Passive Crawling with Burp Suite 


##### SQL Injection with SQLMap 


##### XSS Attack with XSSer

##### Authenticated XSS Attack with XSSer 

##### Attacking HTTP Login From with Hydra 

##### Attacking Basic Auth with Burp Suite 


##### Attacking HTTP Login From ZAProxy 