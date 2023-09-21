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
- `getuid`: Geeft het gebruikers-ID (UID) weer van de huidige gebruiker. Dit kan helpen bij het bepalen van de rechten van de gebruiker.
- `sysinfo`: Levert gedetailleerde informatie over het besturingssysteem en de hardware van het gecompromitteerde systeem. 
- `cd system32` (opgeslagen in `C:\Windows\System32`): Navigeert naar de systeemmap van Windows, wat belangrijk kan zijn om systeemgerelateerde bestanden en instellingen te verkennen.
    - `cat eula.txt`: Toont de inhoud van het "eula.txt"-bestand binnen de System32-map, wat mogelijk nuttige informatie bevat over licenties en gebruik
- get a command shell:  `shell` 
	-  `hostname` 
	- `systeminfo` 
	- more info about hotfixes: 
	  `wmic qfe get Caption,Description,HotFIxID,InstalledOn` Toont details over geïnstalleerde hotfixes en patches op het systeem


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
- `shell`: Biedt toegang tot een interactieve shell op het gecompromitteerde systeem voor verschillende taken.
- `whoami`: Geeft de naam van de huidige gebruiker weer, wat handig is om te weten wie is ingelogd.
- `whoami /priv`: Toont de privileges van de huidige gebruiker, essentieel om de acties die de gebruiker kan ondernemen te begrijpen.
- `query user`: Lijst van ingelogde gebruikers op het systeem, nuttig om andere actieve gebruikers te identificeren.
- `net user administrator`: Geeft informatie over het administrator-account, handig om te weten of het is ingeschakeld en mogelijke kwetsbaarheden te identificeren.
- `net user guest`: Toont informatie over het gastaccount, handig om te weten of het account is ingeschakeld en of het een zwakke plek vormt.
- `net localgroup`: Lijst van lokale gebruikersgroepen op het systeem, helpt bij het identificeren van andere groepen met specifieke rechten.
- `net localgroup administrators`: Toont de leden van de 'Administrators'-groep, belangrijk om te weten wie beheerdersrechten heeft op het systeem


#####  Enumerating Network Information 


Information we are looking for 

- Current IP address & network adapter
- Internal networks 
- TCP/UDP services running and their respective ports 
- Other hosts on the network 
- Routing table
- Windows Firewall state 


Command shell session:
- `shell` (Interactieve toegang tot het systeem om verdere aanvallen uit te voeren.)
- `ipconfig` (Identificeert netwerkconfiguratie voor lateralisatie en informatie over het systeem.)
- `ipconfig /all` (Biedt gedetailleerde netwerkinformatie voor bredere verkenning van het netwerk.)
- `route print` (Onthult routeringstabellen om netwerkverkeer te begrijpen.)
- `arp -a` (Toont IP-naar-MAC-mapping voor het identificeren van aangesloten apparaten.)
- `netstat -ano` (Geeft actieve verbindingen weer en helpt bij het vinden van kwetsbaarheden.)
  
![[Pasted image 20230917174525.png]] 
	our meterpreter session 
	
- `netsh firewall show state` (Onthult de firewallstatus voor potentiële bypassing of regelwijzigingen.)
- `netsh advfirewall firewall` (Beheer van geavanceerde firewallregels om beveiliging te verminderen of verkeer te manipuleren.)

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


Metapsloit 
- nmap -sV -p 5985 ip (target WinRM protocol)
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
- `upload /root/Desktop/jaws-enum.ps1`
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


 





























