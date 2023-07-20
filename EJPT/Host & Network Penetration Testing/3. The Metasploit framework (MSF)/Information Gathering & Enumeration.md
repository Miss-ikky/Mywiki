
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


###### Port scanning 

Auxiliary modules are used to perform functionality like scanning, discovery and fuzzing. We can use it to perform TCP and UDP port scanning as well as enumerating information from services FTP, SSH, HTTP 
	Auxiliary modules can be used during information gathering phase 
Waarom auxiliary modules gebruiken als je ook met nmap kunt scannen? Auxiliary modules zijn erg handig voor **post exploitation phase:**   wanneer je een target hebt geexploit kom je erachter dat deze target onderdeel is van groot netwerk dan heb je nog meer targets die je kunt exploiten through initial exploited server. De auxiliary modules zijn dan handig om verder het interne/private netwerk te scannen. Dit kan niet met nmap omdat je dan nmap moet importeren op geexploite target host of OS. 

![[Pasted image 20230720154107.png]]


- start the postgresql service so we can interact with metasploit database 
- start msfconsole 
- check the status of the database, command: db_status 
- set up workspace, command: workspace -a Port_scan 
- use the auxiliary tcp portscan 
- 
