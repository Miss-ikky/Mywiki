![[Pasted image 20230917164125.png]]
## Windows Local Enumeration 

##### Enumerating System Information 

- Information to gather: 
	- what OS is running?
	- What OS version? 
	- Hostname, 
	- OS Name (e.g. Windows 7, 8)
	- OS Build & service Pack (e.g.Windows 7 SP1 7600)
	- OS Architecture (x64/x86)
		- if the architecture is 32bit then that means that you need to compile a 32bit executable
		- meterpreter payload with msfvenom you need to generate a 32bit payload or 64bit payload
	- Installed updates/Hotfixes 


Meterpreter session: 
- `getuid` 
- `sysinfo` 
- `cd system32 `
	- `cat eula.txt `
- get a command shell:  `shell` 
	-  `hostname` 
	- `systeminfo` 
	- more info about hotfixes: 
	  `wmic qfe get Caption,Description,HotFIxID,InstalledOn`


##### Enumerating Users & Groups

information we are looking for: 
- Current users & priviliges 
- additional user information 
- Other users on the system
- Groups
- Members of the built-in administrator group 

Meterpreter session: 
- `getuid`
- `getprivs`
- currently logged in users: msf module:` use post/windows/gather/enum_logged_on_users ` 
	- set to meterpreter session - run 

command shell session
- `shell` 
- `whoami` 
- `whoami /privs `
- `query user `
- `net user administator`
- `net user guest `
- `net localgroup` 
- `net localgroup administators`


#####  Enumerating Network Information 


Information we are looking for 

- Current IP address & network adapter
- Internal networks 
- TCP/UDP services running and their respective ports 
- Other hosts on the network 
- Routing table
- Windows Firewall state 


Command shell session:
- `shell` 
- `ipconfig` 
-  `ipconfig /all`
- `route print`
- `arp -a`
- `netstat -ano`
![[Pasted image 20230917174525.png]] 
our meterpreter session 

- `netsh firewall show state` 
- `netsh advfirewall firewall











##### Enumerating Processes & Services 

**Process** is an instance of a running executable (.exe) or program -  (launch google chrome)
A **service** is a process which runs in the background and does not interact with the desktop - (not end-user applications or third party applications)

Info we are looking for: 
- Running processes & services 
- Scheduled tasks 


meterpreter:
- `ps` 
	- user column shows priviliges associated with specific processes. 
	- it will show priviliges associated with the user that you hacked 
- `pgrep explorer.exe ` (when no process id is shown it means that the process is not running)
- `migrate process_id`
- `getuid`
- `sysinfo`

Command shell session
- `shell` 
- `net start` (services are listed not processes)
- `wmic service list brief` (list running services)
- `tasklists /SVC` (process running and services running under a process)
- `schtasks /query /fo LIST /v` (scheduled task that have been configured to run on target)
	- scheduled task can be exploited to elevate priviliges, scheduled that are running with nt authority system priviliges, we can let it executable that we specify we can elevate our priviliges. 




















##### Automating Windows Local Enumeration  (metasploit)































