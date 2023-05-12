

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

		In dit geval verwijst 'services' naar de services of diensten die op de doelhost draaien en die worden aangeboden via het SMB (Server Message Block)-protocol op poort 445. Met de Nmap-opdracht wordt de script "smb-enum-services" gebruikt om informatie te verkrijgen over de services die op deze poort worden aangeboden, inclusief hun namen, statussen, types en de besturingselementen die ze accepteren. Deze informatie kan nuttig zijn voor beveiligingsdoeleinden, omdat het kan helpen bij he


 16. run command to see what shares exist but also enumerate those shares: *nmap -p445 --script smb-enum-shares,smb-ls      --script-arg smbusername=adminsitrator, smbpassword=smbserver_771 {ip}*   
	 - De opties "--script smb-enum-shares, smb-ls" specificeren dat twee scripts moeten worden uitgevoerd tijdens de scan: "smb-enum-shares" voor het vinden van gedeelde mappen en "smb-ls" voor het oplijsten van de inhoud van de gevonden mappen.
	 - -   `smb-enum-shares`: This script will enumerate all the SMB shares on the target system.
	-   `smb-ls`: This script will run the `ls` command on each share. Note that the `smb-ls` script requires authentication credentials for the target system. You can provide the credentials using the `smbusername` and `smbpassword` script arguments. For example:
	

-- LAB -- 

Target ip: 10.2.26.153 
**Objective:**

1.  Identify SMB Protocol Dialects

root@attackdefense:~# nmap -p445 --script smb-protocols 10.2.26.153
Nmap scan report for 10.2.26.153

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-protocols: 
|   dialects: 
|     NT LM 0.12 (SMBv1) [dangerous, but default]
|     2.02
|     2.10
|     3.00
|_    3.02


2. .  Find SMB security level information


root@attackdefense:~# nmap -p445 --script smb-security-mode 10.2.26.153
PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)


-   account_used: guest: dit betekent dat de gastaccount wordt gebruikt voor de SMB-sessie. Dit is een veel voorkomende zwakte in de beveiliging, omdat de gastaccount vaak is ingeschakeld en mogelijk zwakke referenties heeft of helemaal geen wachtwoord.
    
-   authentication_level: user: dit betekent dat een wachtwoord vereist is om toegang te krijgen tot de SMB-sessie, wat een hoger beveiligingsniveau impliceert dan de "geen" of "gast" -modi.
    
-   challenge_response: ondersteund: dit betekent dat het challenge-response-authenticatiemechanisme wordt ondersteund. Dit is een protocol dat wordt gebruikt om de identiteit van een gebruiker te verifiëren zonder het wachtwoord zelf te verzenden.
    
-   message_signing: disabled (gevaarlijk, maar standaard): dit betekent dat SMB-berichtondertekening is uitgeschakeld.   Ondertekening van berichten is een beveiligingsfunctie die ervoor zorgt dat berichten niet kunnen worden gewijzigd terwijl ze in transit zijn. Wanneer het is uitgeschakeld, kunnen aanvallers verkeer tussen de client en de server manipuleren en in sommige gevallen vertrouwelijke gegevens stelen of beschadigen.

3. .  Enumerate active sessions, shares, Windows users, domains, services, etc.


**Active sessions:** 

root@attackdefense:~# nmap -p445 --script smb-enum-sessions 10.2.26.153
Starting Nmap 7.91 ( https://nmap.org ) at 2023-05-10 19:44 IST
Nmap scan report for 10.2.26.153
Host is up (0.0031s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-enum-sessions: 
|   Users logged in
|_    WIN-OMCNBKR66MN\bob since 

-> The output indicates that there is one user, "bob," who is currently logged in to the SMB server from a Windows machine with the hostname "WIN-OMCNBKR66MN". The time at which the user logged in is unknown or not reported.


**shares**: 

root@attackdefense:~# nmap -p445 --script smb-enum-shares 10.2.26.153

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-enum-shares: 
|   account_used: guest
|   \\10.2.26.153\ADMIN$: 
|     Type: STYPE_DISKTREE_HIDDEN
|     Comment: Remote Admin
|     Anonymous access: none
|     Current user access: none

|   \\10.2.26.153\C: 
|     Type: STYPE_DISKTREE
|     Comment: 
|     Anonymous access: none
|     Current user access: READ

|   \\10.2.26.153\C$: 
|     Type: STYPE_DISKTREE_HIDDEN
|     Comment: Default share
|     Anonymous access: none
|     Current user access: none

|   \\10.2.26.153\D$: 
|     Type: STYPE_DISKTREE_HIDDEN
|     Comment: Default share
|     Anonymous access: none
|     Current user access: none

|   \\10.2.26.153\Documents: 
|     Type: STYPE_DISKTREE
|     Comment: 
|     Anonymous access: none
|     Current user access: READ

|   \\10.2.26.153\Downloads: 
|     Type: STYPE_DISKTREE
|     Comment: 
|     Anonymous access: none
|     Current user access: READ

|   \\10.2.26.153\IPC$: 
|     Type: STYPE_IPC_HIDDEN
|     Comment: Remote IPC
|     Anonymous access: none
|     Current user access: READ/WRITE

 lijst van SMB-shares getoond die toegankelijk zijn via het IP-adres 10.2.26.153. De lijst bevat de volgende informatie over elke share:

-   Type: het type van de share (bijvoorbeeld STYPE_DISKTREE voor een share die een map op de schijf voorstelt)
-   Comment: een commentaar die de eigenaar van de share heeft toegevoegd
-   Anonymous access: of anonieme toegang mogelijk is voor deze share , je moet bij none authenticaten voor je de share kan bereiken 
-   Current user access: welke toegangsniveaus momenteel beschikbaar zijn voor de ingelogde gebruiker

In dit geval zijn er verschillende shares beschikbaar, waaronder ADMIN$, C:, C$, D:, Documents, Downloads en IPC$. De toegangsniveaus voor elke share variëren afhankelijk van de gebruiker en zijn ingesteld op "READ", "READ/WRITE" of "none".

Het verschil tussen `STYPE_DISKTREE` en `STYPE_DISKTREE_HIDDEN` is dat `STYPE_DISKTREE` aangeeft dat de share publiekelijk zichtbaar is voor alle gebruikers op het netwerk, terwijl `STYPE_DISKTREE_HIDDEN` aangeeft dat de share verborgen is voor gebruikers die de naam van de share niet kennen. Dit betekent dat gebruikers die proberen toegang te krijgen tot een verborgen share deze alleen kunnen bereiken als ze de exacte naam van de share weten, terwijl gebruikers toegang kunnen krijgen tot een publieke share door simpelweg te browsen door het netwerk. Dit kan een extra beveiligingslaag bieden voor gevoelige gegevens of toepassingen die niet publiekelijk toegankelijk moeten zijn.

**Windows users** 

root@attackdefense:~# nmap -p445 --script smb-enum-users -script-args smbusername=administrator,smbpassword=smbserver_771 10.2.26.153

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-enum-users: 
|   WIN-OMCNBKR66MN\Administrator (RID: 500)
|     Description: Built-in account for administering the computer/domain
|     Flags:       Password does not expire, Normal user account
|   WIN-OMCNBKR66MN\bob (RID: 1010)
|     Flags:       Password does not expire, Normal user account
|   WIN-OMCNBKR66MN\Guest (RID: 501)
|     Description: Built-in account for guest access to the computer/domain
|_    Flags:       Password not required, Password does not expire, Normal user account





**Domains** 


root@attackdefense:~# nmap -p445 --script smb-enum-domains --script-args smbusername=administrator,smbpassword=smbserver_771  10.2.24.168

Nmap scan report for 10.2.24.168


PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-enum-domains: 
|   WIN-OMCNBKR66MN
|     Groups: WinRMRemoteWMIUsers__
|     Users: Administrator, bob, Guest
|     Creation time: 2013-08-22T14:47:57
|     Passwords: min length: n/a; min age: n/a days; max age: 42 days; history: n/a passwords
|     Properties: Complexity requirements exist
|     Account lockout disabled
|   Builtin
|     Groups: Access Control Assistance Operators, Administrators, Backup Operators, Certificate Service DCOM Access, Cryptographic Operators, Distributed COM Users, Event Log Readers, Guests, Hyper-V Administrators, IIS_IUSRS, Network Configuration Operators, Performance Log Users, Performance Monitor Users, Power Users, Print Operators, RDS Endpoint Servers, RDS Management Servers, RDS Remote Access Servers, Remote Desktop Users, Remote Management Users, Replicator, Users
|     Users: n/a
|     Creation time: 2013-08-22T14:47:57
|     Passwords: min length: n/a; min age: n/a days; max age: 42 days; history: n/a passwords
|_    Account lockout disabled

-   Two domains are listed in the output: `WIN-OMCNBKR66MN` and `Builtin`.
-   The `WIN-OMCNBKR66MN` domain is shown to have a group called `WinRMRemoteWMIUsers__` and three users: `Administrator`, `bob`, and `Guest`.
-   The domain creation time is also shown, along with information about password policies, account lockout settings, and other properties.
-   The `Builtin` domain is shown to have several built-in groups and no users.
-   Again, the domain creation time is shown along with password policies and account lockout settings.

**Services**: 

root@attackdefense:~# nmap -p445 --script smb-enum-services --script-args smbusername=administrator,smbpassword=smbserver_771  10.2.24.168

Nmap scan report for 10.2.24.168
Host is up (0.0025s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds
| smb-enum-services: 
|   AmazonSSMAgent: 
|     display_name: Amazon SSM Agent
|     state: 
|       SERVICE_PAUSED
|       SERVICE_PAUSE_PENDING
|       SERVICE_RUNNING
|       SERVICE_CONTINUE_PENDING
|     type: 
|       SERVICE_TYPE_WIN32
|       SERVICE_TYPE_WIN32_OWN_PROCESS
|     controls_accepted: 
|       SERVICE_CONTROL_NETBINDADD
|       SERVICE_CONTROL_CONTINUE
|       SERVICE_CONTROL_PARAMCHANGE
|       SERVICE_CONTROL_STOP
|       SERVICE_CONTROL_NETBINDENABLE
|       SERVICE_CONTROL_INTERROGATE
|   AWSLiteAgent: 
|     display_name: AWS Lite Guest Agent
|     state: 
|       SERVICE_PAUSED
|       SERVICE_PAUSE_PENDING
|       SERVICE_RUNNING
|       SERVICE_CONTINUE_PENDING
|     type: 
|       SERVICE_TYPE_WIN32
|       SERVICE_TYPE_WIN32_OWN_PROCESS
|     controls_accepted: 
|       SERVICE_CONTROL_NETBINDADD
|       SERVICE_CONTROL_CONTINUE
|       SERVICE_CONTROL_PARAMCHANGE
|       SERVICE_CONTROL_STOP
|       SERVICE_CONTROL_NETBINDENABLE
|       SERVICE_CONTROL_INTERROGATE
|   DiagTrack: 
|     display_name: Diagnostics Tracking Service
|     state: 
|       SERVICE_PAUSED
|       SERVICE_PAUSE_PENDING
|       SERVICE_RUNNING
|       SERVICE_CONTINUE_PENDING
|     type: 
|       SERVICE_TYPE_WIN32
|       SERVICE_TYPE_WIN32_OWN_PROCESS
|     controls_accepted: 
|       SERVICE_CONTROL_NETBINDADD
|       SERVICE_CONTROL_CONTINUE
|       SERVICE_CONTROL_PARAMCHANGE
|       SERVICE_CONTROL_STOP
|       SERVICE_CONTROL_NETBINDENABLE
|       SERVICE_CONTROL_INTERROGATE
|   Ec2Config: 
|     display_name: Ec2Config
|     state: 
|       SERVICE_PAUSED
|       SERVICE_PAUSE_PENDING
|       SERVICE_RUNNING
|       SERVICE_CONTINUE_PENDING
|     type: 
|       SERVICE_TYPE_WIN32
|       SERVICE_TYPE_WIN32_OWN_PROCESS
|     controls_accepted: 
|       SERVICE_CONTROL_NETBINDADD
|       SERVICE_CONTROL_CONTINUE
|       SERVICE_CONTROL_PARAMCHANGE
|       SERVICE_CONTROL_STOP
|       SERVICE_CONTROL_NETBINDENABLE
|       SERVICE_CONTROL_INTERROGATE
|   MSDTC: 
|     display_name: Distributed Transaction Coordinator
|     state: 
|       SERVICE_PAUSED
|       SERVICE_PAUSE_PENDING
|       SERVICE_RUNNING
|       SERVICE_CONTINUE_PENDING
|     type: 
|       SERVICE_TYPE_WIN32
|       SERVICE_TYPE_WIN32_OWN_PROCESS
|     controls_accepted: 
|       SERVICE_CONTROL_NETBINDADD
|       SERVICE_CONTROL_CONTINUE
|       SERVICE_CONTROL_PARAMCHANGE
|       SERVICE_CONTROL_STOP
|       SERVICE_CONTROL_NETBINDENABLE
|       SERVICE_CONTROL_INTERROGATE
|   Spooler: 
|     display_name: Print Spooler
|     state: 
|       SERVICE_PAUSED
|       SERVICE_PAUSE_PENDING
|       SERVICE_RUNNING
|       SERVICE_CONTINUE_PENDING
|     type: 
|       SERVICE_TYPE_WIN32
|       SERVICE_TYPE_WIN32_OWN_PROCESS
|     controls_accepted: 
|       SERVICE_CONTROL_NETBINDADD
|       SERVICE_CONTROL_CONTINUE
|       SERVICE_CONTROL_STOP
|_      SERVICE_CONTROL_NETBINDENABLE


-   The smb-enum-services script output is then displayed, showing a list of services running on the target system that were discovered during the scan.
-   For each service discovered, the script shows the following information:
    -   The display name of the service (e.g., Amazon SSM Agent, AWS Lite Guest Agent, etc.).
    -   The current state of the service, which can be one of SERVICE_PAUSED, SERVICE_PAUSE_PENDING, SERVICE_RUNNING, or SERVICE_CONTINUE_PENDING.
    -   The type of the service, which can be one of SERVICE_TYPE_WIN32 or SERVICE_TYPE_WIN32_OWN_PROCESS.
    -   The controls that the service accepts, which can include SERVICE_CONTROL_NETBINDADD, SERVICE_CONTROL_CONTINUE, SERVICE_CONTROL_PARAMCHANGE, SERVICE_CONTROL_STOP, SERVICE_CONTROL_NETBINDENABLE, and SERVICE_CONTROL_INTERROGATE.

Based on this output, we can see that there are several services running on the target system, including the Amazon SSM Agent, AWS Lite Guest Agent, Diagnostics Tracking Service, Ec2Config, Distributed Transaction Coordinator, and Print Spooler. This information can be useful for further enumeration or exploitation of the target system.

-   **Current state of the service**: This shows the current state of the service, which can be one of the following:
    
    -   SERVICE_PAUSED: The service is currently paused.
    -   SERVICE_PAUSE_PENDING: The service is in the process of pausing.
    -   SERVICE_RUNNING: The service is currently running.
    -   SERVICE_CONTINUE_PENDING: The service is in the process of continuing.
-   **Type of the service**: This shows the type of the service, which can be one of the following:
    
    -   SERVICE_TYPE_WIN32: The service runs in a shared process.
    -   SERVICE_TYPE_WIN32_OWN_PROCESS: The service runs in its own process.
-   **Controls that the service accepts**: This shows the controls that the service accepts, which can include the following:
    
    -   SERVICE_CONTROL_NETBINDADD: The service can bind to new network addresses.
    -   SERVICE_CONTROL_CONTINUE: The service can continue from a paused state.
    -   SERVICE_CONTROL_PARAMCHANGE: The service can receive notifications of parameter changes.
    -   SERVICE_CONTROL_STOP: The service can be stopped.
    -   SERVICE_CONTROL_NETBINDENABLE: The service can bind to all network addresses.
    -   SERVICE_CONTROL_INTERROGATE: The service can receive status requests.


##### SMB: SMBMap 

1. ping the target ip to check if it is alive 
   
2. run an nmap scan: *nmap {target ip}*
   
3. run an script voor smb protocols: *nmap -p445 --script smb-protocols {targetip}*  this will tell us the procols and dialects. When we spot dialect SMBv1 which is dangerous we will use smbmap
   
4.  run smbmap voor smbv1: *smbmap -u guest -p " " -d . -H {target ip}* 
	- -   `-u guest`: gebruikt de gebruikersnaam "guest" om verbinding te maken met de host.
	-   `-p " "`: stelt het wachtwoord in op een lege string, wat betekent dat er geen wachtwoord wordt gebruikt om verbinding te maken met de host.
	-   `-d .`: stelt de domeinnaam in op "." (de huidige domeinnaam).
	-   `-H {target ip}`: specificeert het IP-adres van de doel-SMB-host waarmee verbinding moet worden gemaakt.
	- The results will be shares that the guest is able to connect to 
	
5. Try to run a command: *smbmap -H {taget ip} -u administrator -p smbserver_771 -x 'ipconfig' *
	- De optie `-x` wordt gebruikt om een opdracht uit te voeren op de SMB-host nadat er verbinding is gemaakt. In dit geval wordt de opdracht 'ipconfig' uitgevoerd, die de netwerkconfiguratie van de host weergeeft.
	  
6. run a command to see a list of drives and folders     : *smbmap -H {taget ip} -u administrator -p 'smbserver_771' -L *
	- Door dit commando uit te voeren, krijg je een lijst van gedeelde mappen te zien die beschikbaar zijn op de opgegeven SMB-server met behulp van de opgegeven inloggegevens. 
	-   -L: Deze optie instrueert smbmap om een lijst van gedeelde mappen op de SMB-server weer te geven.

7. if there is a C$ we will try to connect with it using the following command:   *smbmap -H {taget ip} -u Administrator -p 'smbserver_771' -r 'C$'*
	- Door dit commando uit te voeren, maak je verbinding met de opgegeven SMB-server met de opgegeven inloggegevens en krijg je toegang tot de inhoud van de 'C$' gedeelde map, waarbij je informatie kunt zien over de bestanden en mappen die zich op de systeemschijf van de SMB-server bevinden.
	- -r 'C$ Deze optie geeft aan dat je toegang wilt krijgen tot de gedeelde map C$ op de SMB-server. 'C$' is een verborgen gedeelde map op Windows-systemen die meestal verwijst naar de systeemschijf (meestal de C-schijf)
	  
8. Lets try the following: 
	1. create a file called backdoor: *touch backdoor*  check with ls to see if the file exist 
	2.  run the following command: *smbmap -H {taget ip} -u Administrator -p 'smbserver_771' --upload '/root/backdoor' 'C$\backdoor'  * 
				- --upload '/root/backdoor' 'C$\backdoor'`: This option is used to upload a file from the local machine to the remote server. It specifies the source file path (`/root/backdoor`) on the local machine and the destination file path (`C$\backdoor`) on the remote server. In this example, it is attempting to upload the file located at `/root/backdoor` to the `C:\backdoor` path on the remote server.
	1. 