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


- upgrade the shell session to a meterpreter session
- ``sysinfo`` 
- `shell`
- `/bin/bash -i`
- navigate to root of home directory: `cd /root`
	- `hostname`
	- `cat /etc/issue`
	- `cat /etc/*release`
	- `uname -a `
	- `env`
	- `ls cpu `
	- `free -h `
	- `df -h`
	- `df -ht ext4`
	- `lsblk | grep sd `
	- for debian: `dpkg -l `
	


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
- ` ls -al /home `
- `groups` 
- `groups user_name`
- `who` 
- `last`  (legitimate connections by other users on system)
- `lastlog` (ssh or fysically)




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
	- `ifconfig` 
	- `ip a s`
	- `cat /etc/networks
	- `cat /etc/hosts `
	- `cat /etc/resolv.conf `
	- `arp -a `
- `arp` 
- help in meterpreter, look at networking command 
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







##### Automating Linux Local Enumeration 


`/gettime.cgi `script is vulnerable for shellshock 
`exploit/multi/http/apache_mod_cgi_bash_env_exec `

Useful metasploit modules for enumeration (post exploitations modules need active session):

- post/linux/gather/enum_configs   (`open files of interest via cat`)
- post/linux/gather/enum_network 
- post/linux/gather/enum_system 
- post/linux/gather/enum_checkvm 


- LinEnum is a simple bash script that automates common Linux local enumeration check in addition to identifying privilege scalation vulnerabilities. 
	- Github: https://github.com/rebootuser/LinEnum 


- upload exploit files and scripts to temp directory (small possibility of being discovered )



















