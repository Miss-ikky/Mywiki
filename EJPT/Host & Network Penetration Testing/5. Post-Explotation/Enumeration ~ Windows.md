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






##### Automating Windows Local Enumeration  

Metasploit

Metapsloit 
- nmap -sV -p 5958 ip (target WinRM protocol)
- metasploit module winrm_script_exec 
	- set FORCE_VBS ture 
	
	- `sysinfo` 
	- `getuid`
	- `show_mount`

- metasploit module win_privs (post-exploitation module so set session)
- metasploit module enum_logged_on_users (post-exploitation module so set session)
- metasploit module checkvm (post-exploitation module so set session)
- metasploit module enum_applications (post-exploitation module so set session)
- metasploit module enum_computers  (post-exploitation module so set session)
- metasploit module enum_patches (post-exploitation module so set session)
- metasploit module enum_shares (post-exploitation module so set session)


- WIndows Local Enum script
	- **JAWS** (Just another Windows Enum script) is a powershell script quickly identify potential privilege escalation vectors on Windows Systems. It is written using Powershell 2.0 so should run on every Windows version Windows 7
	- https://github.com/411Hall/JAWS

jaws.enum.ps1 script (copy the code and paste in the lab-environment, (control shift,alt opens clipboard) paste in textfile, save as jaws-enum.ps1)
- open meterpreter session
- navigate to root of C drive: `cd C:\\`
- `dir` 
- ALWAYS save your files you upload to target in temp directory (if the temp directory does not exit `mkdir Temp`) 
- `cd Temp`
- `upload /rppt/Desktop/jaws-enum.ps1`
- `shell`
- `dir`
- execute powershell from within command shell session: `powershell.exe -ExecutionPolicy BYpass -File .\jaws-enum.ps1 -OutputFilename JAWD-Enum.txt `
	- - `powershell.exe`: Launches PowerShell.
	- `-ExecutionPolicy Bypass`: Temporarily bypasses execution policies (default users are not allowed the execute powershell.
	- `-File .\jaws-enum.ps1`: Executes the script "jaws-enum.ps1" in the current directory.
	- `-OutputFilename JAWD-Enum.txt`: Specifies the output file name for the script's output. Contains results of the script executed. 
- download the file to kali linux system: control c to stop the command shell and get back to meterpreter session
	- `ls`
	- `download jaws-enum.txt` will download to root of kali linux system 


 





























