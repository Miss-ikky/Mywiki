

##### Servers & services 

Server:  it services something to a user, provides some sort of functionality that can be utilized by other devices. 
- We pull resources from servers (e.g. email/files). 
- Server can be runing windows, linux, macos. every computer can be a server 
- servers need to be accessed remotely 

Services (program on a computer that does a specific function) running on a server: 
- require poning a listening port on the server and accepting connections remotly. 
- open the barrier and allow access to programm remotely 
- problem: misconfig or bugs that can give remote access to whole system 


Examples of services that you would see on a server 

#### SMB 
- Windows file share implementation 
- CIFS (generic version) works the same as SMB 
- port 445 
- netbios sets up session for smb 
- SMB recon: 
-  Run an nmap scan for open ports ![[Pasted image 20230509105853.png]] Nmap ‘--open’ option would show only exposed ports of the live hosts
  
- Run scan for service enumeration on our OS 
![[Pasted image 20230509104119.png]]
- nmap {target ip} -sV -sC  (sC runs basic scripts) ![[Pasted image 20230509104300.png]]
   helpfull smb enumeration 
- net use in powershell for smb ![[Pasted image 20230509105010.png]]


SMB Discover and Mount 
- Objective: Learn to use Nmap to scan the target machine and mount the SMB share of the target machine using the Windows File Explorer as well as using the command prompt.  Objective: Discover SMB share and mount it
---
Lab SMB: 

1. ipconfig to get ip and check cidr 
2. Run Nmap scan against the subnet to discover the target machine’s IP address:  *nmap -T4 10.2.29.125/20 --open*

Nmap scan report for ip-10-2-16-110.eu-central-1.compute.internal (
Host is up (0.010s latency).
443/tcp open  https

Nmap scan report for ip-10-2-18-61.eu-central-1.compute.internal (1
Not shown: 991 closed ports
PORT      STATE SERVICE
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
**445/tcp   open  microsoft-ds**
3389/tcp  open  ms-wbt-server
49152/tcp open  unknown
49153/tcp open  unknown
49154/tcp open  unknown
49155/tcp open  unknown
49165/tcp open  unknown
MAC Address: 02:A3:CA:EC:C9:AE (Unknown)

Nmap scan report for ip-10-2-19-34.eu-central-1.compute.internal (1
Host is up (0.0091s latency).
Not shown: 999 filtered ports
Some closed ports may be reported as filtered due to --defeat-rst-r
PORT    STATE SERVICE
443/tcp open  https
MAC Address: 02:8D:74:B4:D5:18 (Unknown)

Nmap scan report for ip-10-2-30-254.eu-central-1.compute.internal (
Host is up (0.0024s latency).
Not shown: 999 filtered ports
Some closed ports may be reported as filtered due to --defeat-rst-r
PORT    STATE SERVICE
443/tcp open  https
MAC Address: 02:EF:27:B5:9F:2A (Unknown)

Nmap scan report for ip-10-2-29-125.eu-central-1.compute.internal (
Host is up (0.00s latency).
Not shown: 990 closed ports
PORT     STATE SERVICE
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
**445/tcp  open  microsoft-ds**
1025/tcp open  NFS-or-IIS
1026/tcp open  LSA-or-nterm
1027/tcp open  IIS
1028/tcp open  unknown
1037/tcp open  ams
1050/tcp open  java-or-OTGfileshare
3389/tcp open  ms-wbt-server 

---

**nmap 10.2.18.61 -sV -O**

Nmap scan report for ip-10-2-18-61.eu-central-1.compute.inter

PORT      STATE SERVICE            VERSION
135/tcp   open  msrpc              Microsoft Windows RPC
139/tcp   open  netbios-ssn        Microsoft Windows netbios-
**445/tcp   open  microsoft-ds       Microsoft Windows Server 2**
3389/tcp  open  ssl/ms-wbt-server?
49152/tcp open  msrpc              Microsoft Windows RPC
49153/tcp open  msrpc              Microsoft Windows RPC
49154/tcp open  msrpc              Microsoft Windows RPC
49155/tcp open  msrpc              Microsoft Windows RPC
49165/tcp open  msrpc              Microsoft Windows RPC

Network Distance: 1 hop
Service Info: OSs: **Windows, Windows Server 2008 R2 - 2012**; CP

OS and Service detection performed. Please report any incorre
Nmap done: 1 IP address (1 host up) scanned in 78.05 seconds


---

 **nmap 10.2.18.61 -sV -sC**
PORT      STATE SERVICE            VERSION
135/tcp   open  msrpc              Microsoft Windows RPC
139/tcp   open  netbios-ssn        Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds       Windows Server 2012 R2 Standard 9600 microsoft-ds
3389/tcp  open  ssl/ms-wbt-server?
49152/tcp open  msrpc              Microsoft Windows RPC
49153/tcp open  msrpc              Microsoft Windows RPC
49154/tcp open  msrpc              Microsoft Windows RPC
49155/tcp open  msrpc              Microsoft Windows RPC
49165/tcp open  msrpc              Microsoft Windows RPC
MAC Address: 02:A3:CA:EC:C9:AE (Unknown)
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:window

Host script results:
nbstat: NetBIOS name: WIN-OMCNBKR66MN, NetBIOS user: NetBIOS MAC: 02:a3:ca

smb-os-discovery: 
smb-security-mode: 
smb2-security-mode:


##### SMB: Nmap scripts

- Once you establish the existence of the smb server now we procede with enumeration which invovles finding more info about the service (what can we learn from it and what can we do with it)
- nmap has an scripting engine (see documentation of nmap)
	- to use scripts we use: *--script {scriptname}*
- stappenplan: 

1. ping target ip to check connection  
2.  run an nmap scan:  *nmap {target ip}*
3. check if it has smb (port 445 is open)
4. run an nmap scan just on the smb port 445 where we look at the smb protocol: *nmap -p445 --script smb-protocols {target ip}*
5. run an nmap scan just on smb port 445 where we check the securty mode: *nmap -p445 --script smb-security-mode {target ip}*
6. run an nmap scan where we enumerate a session: *nmap -p445 --script smb-enum-sessions {target ip}*
	- smb-eum-sessions: geeft Nmap opdracht om het script smb-enum-sessions uit te voeren, wat informatie over actieve sessies in het SMB-protocol kan opleveren
7. run the following nmap: *nmap -p445 --script smb-enum-sessions --script-args* *smbusername={adminstrator}, smbpassword={smbserver_771} {target ip}*
	 - script-args: geeft argumenten door aan het smb-enum-sessions script, die specifiek zijn voor dat script. 
8. run the following nmap command to enumerate the shares: *nmap -p445 --script smb-enum-shares {target ip}* 
	- gebruikt om de gedeelde bronnen te identificeren die beschikbaar zijn op een bepaalde host die SMB gebruikt op poort 445. SMB is een protocol dat wordt gebruikt voor het delen van bestanden en printers tussen computers op een netwerk
	- .![[Pasted image 20230510145104.png]]
	- ipc$ is usefull because it is a null session (anonymous session) where you can get in as a **guest** account but also utilize this session: IPC$ is een speciale gedeelde bron op computers die het SMB-protocol gebruiken. Het is een null sessie, wat betekent dat er geen inloggegevens vereist zijn om toegang te krijgen tot deze gedeelde bron. Het kan worden gebruikt door geautoriseerde gebruikers om verbinding te maken met het bestandssysteem en printers van de computer via het netwerk, zonder dat ze een geldige inlognaam of wachtwoord nodig hebben.  mogelijk is dat kwaadwillende gebruikers deze null sessie gebruiken om ongeautoriseerde toegang te krijgen tot de bronnen van een computer.
9. Now run a command where we enumerate the shares after authenticating: *nmap -p445  --script smb-enum-shares --script-args smbusername=administrator, smbpaswordsmbserver_771 {target ip}*
10. now enumerate the users to find out which users exist: *nmap -p445 --script smb-enum-users 
     -script-args smbusername=administrator smbpasword=smbserver_771 {target ip}* ![[Pasted image 20230510150118.png]]
     (bult in accounts for guests en password does not expire = misconfiguration). De gastaccount is een ingebouwde account in Windows-besturingssystemen en is bedoeld voor gebruikers die de computer of het netwerk bezoeken zonder geldige inloggegevens te hebben. De account biedt beperkte toegang tot bepaalde bronnen, zoals bestanden en printers, en heeft geen wachtwoord nodig om toegang te krijgen
 11. run a command to look at server statistic: *nmap -p445 --script smb-server-stats --script-arg smbusersname=administrator, smbpassword=smbserver_771 {target ip}*
 13. run command to check what domains exist: *nmap -p445 -script smb-enum-domains -s-cript-args smbusername=administrator, smbpassword=smbserver_771 {target ip}*
 14. run command to check groups: *nmap -p445 --script smb-enum-groups --script-arg smbusername=adminsitrator, smbpassword=smbserver_771 {ip}* 
 15. run command to check what services are running: *nmap -p445 --script smb-enum-services      --script-arg smbusername=adminsitrator, smbpassword=smbserver_771 {ip}*  
 16. run command to see what shares exist but also enumerate those shares: *nmap -p445 --script smb-enum-shares,smb-ls      --script-arg smbusername=adminsitrator, smbpassword=smbserver_771 {ip}*   
	 - De opties "--script smb-enum-shares, smb-ls" specificeren dat twee scripts moeten worden uitgevoerd tijdens de scan: "smb-enum-shares" voor het vinden van gedeelde mappen en "smb-ls" voor het oplijsten van de inhoud van de gevonden mappen.

-- LAB -- 

Target ip: 10.2.26.153 
**Objective:**

1.  Identify SMB Protocol Dialects
2.  Find SMB security level information
3.  Enumerate active sessions, shares, Windows users, domains, services, etc.











