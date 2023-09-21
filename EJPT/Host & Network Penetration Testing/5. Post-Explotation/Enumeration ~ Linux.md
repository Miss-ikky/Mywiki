![[Pasted image 20230917164125.png]]

**vsftpd_234_backdoor**
##### Enumerating System Information 


Information:
- Hostname
- Distribution & distribution release version
- Kernel version & architecture 
- CPU information 
- Disk information & mounted drives 
- Installed packages/software
192.107.143.2/24

- upgrade the shell session to a meterpreter session
- ``sysinfo`` 
- `shell`
- `/bin/bash -i`
- navigate to root of home directory: `cd /root'
	- `hostname` (Display hostname):** This command reveals the hostname of the compromised system. It's interesting because knowing the hostname can help attackers identify the target system and its role in a network.
	- `cat /etc/issue` (Display distribution issue file):** Displays information about the Linux distribution, version, and release. It's useful for understanding the specific distribution running on the compromised system, which can guide further exploitation or post-exploitation activities.
	- `cat /etc/*release` (Display release information):** Similar to the previous command, this provides distribution and version details. It helps in identifying the Linux distribution and release, which may be important for finding and exploiting vulnerabilities.
	- `uname -a` (Display system information):** This command shows detailed information about the system, including the kernel version and system architecture. Attackers use it to gather system-specific details for potential vulnerabilities.
	- `env` (Display environment variables):** Reveals environment variables, which can contain valuable information about the system's configuration and may include sensitive data such as passwords or API keys.
	- `lscpu` (List files or directories in 'cpu' directory):** Lists the contents of the 'cpu' directory. This is interesting if there are specific files or configurations in that directory that may be relevant to the attack or post-exploitation activities.
	- `free -h` (Display memory usage):** Provides information about the system's memory usage. Knowing available memory can be important for planning resource-intensive tasks or attacks.
	- `df -h` (Display disk space usage):Shows disk space usage across mounted file systems. Understanding disk space availability is essential for identifying storage constraints or opportunities for data exfiltration.
	- `df -ht ext4` (Display ext4 file system usage): Lists disk space usage specifically for ext4 file systems. Useful for focusing on a particular file system type when assessing storage resources.
	- `lsblk | grep sd` (List block devices containing 'sd'): Lists block devices containing 'sd' in their names. This can help identify storage devices, which is relevant for data discovery and manipulation.
	- `dpkg -l` (List installed packages - Debian): For Debian-based systems, this command lists all installed packages. It's valuable for identifying installed software and potential targets for exploitation or privilege escalation in a Debian environment.

##### Enumerating User & Groups

information 
- current user & priviliges 
- Other users on the system 
- Groups 


Meterpreter 
- `getuid` (=0 is root, windows= administator and linux=root)
- `shell` 
- `/bin/bash -i `
- `cd /root `
- `whoami`
- `groups root`  (or `groups user_name`)
- `cat /etc/passwd `
	- lists both user accounts and service accounts: 
		Hoe weet je welke een user/service account is? als er een bin/bash of bin/sh (bourne shell) achter een account staat is het een user account, als het een service account is staat er achter sbin/nologin 
                 ![[Pasted image 20230918165340.png]]

- `cat /etc/passwd | grep -v /nologin ` (not show service accounts)
- ` ls -al /home ` Get list of files and directories in /home
- `groups` lists the users group memberships
- `groups user_name` lists group memberships of specific group
- `who` displays info about currently logged-in users 
- `last`  (legitimate connections by other users on system)
- `lastlog` (login via ssh or physically)




##### Enumerating Network Information

information 
- Current IP address & network adapter
- Internal networks
- TCP/UDP services running an their respective ports
- Other hosts on the network 


Meterpreter session 
- `ifconfig`
- `netstat` 
- `route` 
- `shell` 
	- `ifconfig`: Geeft informatie over de netwerkinterfaces van het systeem, inclusief IP-adressen en MAC-adressen.
	- `ip a s`: Toont gedetailleerde informatie over netwerkinterfaces, inclusief IP-adressen en status. (Beschouwd als een modernere en uitgebreidere versie van `ifconfig`.)
	- `cat /etc/networks`: Laat informatie zien over gedefinieerde netwerken in het systeem. (Kan helpen bij het begrijpen van beschikbare netwerkconfiguraties.)
	- `cat /etc/hosts`: Toont de inhoud van het hostbestand, dat host-naamkoppelingen naar IP-adressen definieert. (Handig om aangepaste host-naamkoppelingen te controleren die kunnen worden gebruikt voor doelsystemen of om verkeer om te leiden.)
	- `cat /etc/resolv.conf`: Geeft informatie over DNS-configuratie op het systeem, inclusief DNS-serveradressen. (Kan helpen bij het identificeren van gebruikte DNS-servers/name-server voor netwerkcommunicatie en DNS-verzoekmanipulatie.)
	- `arp -a`: Toont de ARP-cache van het systeem, die IP-adres-naamkoppelingen bevat. (Nuttig voor het ontdekken van andere apparaten in het lokale netwerk en het identificeren van mogelijke doelen voor verdere verkenning of aanvallen.)
	- `arp` help in meterpreter, look at networking command 
`


##### Enumerating Processes & Cron Jobs

Information 
- Running services, processes 
- Cron Jobs 

Meterpreter:
- ? (look at system commands)
- `ps` 
- `pgrep process_name 
- `shell`
	- top
	- `/bin/bash -i `
	- `ps` 
	- `ps aux` 
	- `crontab -l`
	- `ls -al /etc/cron*`
	- `cat /etc/cron*`
- `ps aux | grep root`  (processes that belong to user root)
- `ps aux | grep program_name`

- `ps` (Displays information about running processes on a Unix-like operating system. Interesting in post-exploitation because it allows an attacker to gather information about the running processes on the compromised system, potentially identifying vulnerabilities or valuable targets.)
- `pgrep process_name` (Searches for and lists the process IDs (PIDs) of processes that match a given name. Interesting in post-exploitation because it helps identify specific processes by name, which can be useful for targeting or interacting with them.)
- `shell`
    - `top` (Displays real-time system information, including running processes. Interesting in post-exploitation for monitoring system activity.)
    - `/bin/bash -i` (Starts an interactive Bash shell. Interesting in post-exploitation because gaining access to a shell is a pivotal step for further interaction and control over the compromised system.)
    - `ps` (Displays information about running processes.)
    - `ps aux` (Displays detailed information about all running processes.)
    - `crontab -l` (Lists the cron jobs of the current user. Interesting in post-exploitation for identifying scheduled tasks.)
    - `ls -al /etc/cron*` (Lists cron-related files in the /etc directory. Interesting in post-exploitation for examining cron configurations.)
    - `cat /etc/cron*` (Displays the contents of cron-related files.)
- `ps aux | grep root` (Combines `ps` with `grep` to list processes owned by the user "root." Interesting in post-exploitation because it helps identify processes running with elevated privileges, which may be crucial for escalating privileges or targeting critical system components.)
- `ps aux | grep program_name` (Lists processes and filters them based on a specific program name (`program_name`). Interesting in post-exploitation because it allows the attacker to identify and target processes associated with a particular program or service, potentially for further exploitation or manipulation.)


cat /etc/shells 


##### Automating Linux Local Enumeration 


`/gettime.cgi `script is vulnerable for shellshock 
`exploit/multi/http/apache_mod_cgi_bash_env_exec `

Useful metasploit modules for enumeration (post exploitations modules need active session):

- post/linux/gather/enum_configs   (`open files of interest via cat`)
- post/linux/gather/enum_network 
- post/linux/gather/enum_system 
- post/linux/gather/enum_checkvm 



LinEnum is a simple bash script that automates common Linux local enumeration check in addition to identifying privilege scalation vulnerabilities. 
	- Github: https://github.com/rebootuser/LinEnum 
- upload exploit files and scripts to temp directory (small possibility of being discovered )
- ga naar script in github - raw data - copy the script code into text file in lab environment  - save it on desktop folder of root user as LinEnum.sh (bc its a bash script)
  
  In meterpreter 
- `upload /root/Desktop/LinEnum.sh `
- `shell` 
- list content of temp directory 
- `chmod -x LinEnum.sh `
- `./LinEnum.sh` (execute the script)

























