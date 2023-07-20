The basics: 

- Interface: methods of interacting with the Metasploit Framework 
- Module: Piece of code that perform a particular task, an example of a module is an exploit 
- Exploit: Piece of code/module that is used to take advantage of a vulnerability within a system, service or application 
- Payload - piece of code delivered to the target system by an exploit with the objective of executing arbitrary commands or providing remote access to an attacker 
- Listener: A utility that listens for an incoming connection from a target 

Metasploit framework interfaces 
- msfconsole 
- Armitage is a GUI voor MSF console 


Framework Architecture 
![[Pasted image 20230719174858.png]]

A module in the context of MSF is a piece of code that can be utilized by the MSF. The MSF libraries facilitate the execution of modules without having to write the code necessary in order to execute them 

Lets look into different kind of modules: 
- Exploit modules: A module that is used to take advantage of vulnerability and is typically paired with a payload 
- Payload module: Code that is delivered by MSF and remotely executed on the target after successful exploitation. An example of a payload is a reverse shell that initiates a connection from the target system back to the attacker
- Encoder module: used to encode payloads in order to avoid AV detection, for example skhikata_ga_nai is used to encode Windows payloads 
- NOPS: used to ensure that payloads sizes are consistent and ensure the stability of a payload when executed 
- Auxiliary: A module that is used to perform additional functionality like port scanning and enumeration 

MSF Payload Types 
When we work with exploits, MSF provides you with two types of payloads that can be paired with an exploit module: 

| Payload Type       | Description                                                                                                                                                                                                                                                                                                      |
| ------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Non-staged payload | A payload that is sent to the target system as is, along with the exploit. It does not require multiple parts for execution. Once the payload reaches the target system, it is executed directly.                                                                                                                |
| Staged payload     | A staged payload is sent to the target system in two parts:                                                                                                                                                                                                                                                      |
|                    | 1. The stager: Contains a payload that is used to establish a reverse connection back to the attacker. The stager is usually small and designed to be quickly executed on the target system. Its main purpose is to set up the communication channel between the target and the attacker.                        |
|                    | 2. The stage: Once the stager has successfully established a connection, it downloads the second part of the payload, known as the "stage." The stage is typically more extensive than the stager and carries out the main objectives of the attack. It is executed on the target system after being downloaded. |

![[staged-nonstaged-payloads-4219606287.png]]

Staged payload: 
1. Stagers : used to establish a stable communication channel between the attacker and target, after which a stage payload is downloaded and executed on the target system 
2. Stage : Payload components that are downloaded by the stager 

Meterpreter payload 
The meterpreter is executed in memory on the target system making it difficult to detect. It communicates over a stager socket and provides an attacker with an interactive command interpreter on the target system that facilitates the execution of system commands, file system navigation, keylogging and more. 
"In memory" verwijst naar de toestand van een programma of code die rechtstreeks in het RAM (Random Access Memory) van de computer wordt geladen en uitgevoerd, zonder naar de harde schijf te worden geschreven. 

MSF module location (where is it stored?)
![[Pasted image 20230719182610.png]]

![[Pasted image 20230719183100.png]] 
exploits are stored under OS (in kali select bij view show hidden)


MSF Console with Pentest 
- faces in penetration test (PTES) 
	- PTEST methodology 

![[Pasted image 20230719183735.png]]
 
![[Pasted image 20230719184047.png]]


##### Metasploit Fundamentals 

Installing & configuring the Metasploit framework
- the metasploit database (msfDB) is the main database that is used by metasploit to keep track of all your assessments, host data scans etc. 
- the metasploit framework uses PostgreSQL as the primary database server therefore you need to ensure that the PostgreSQL database service is running and configured correctly 
- msfDB also stored scan results from third party tools like nmap 

installation steps 
1. update repo and upgrade metasploit framework to latest version
   Command: sudo apt-get update && sudo apt-get install metasploit-framework -y
2. start and enable PostgreSQL 
   Command: sudo systemctl enable postgresql 
   Command: sudo systemctl start postgresql 
3. Initialize the metasploit framework database (msfdb)
      Command: sudo msfdb 
      Command: sudo msfdb init    (or reinit)
      Command: sudo msfdb status (to check if it is active)
4. launch msfconsole 
      Command: msfconsole 

MSFconsole Fundamentals - how to utlilize the metasploit console 



1. How to search for modules 
  Command: search \[keyword]  
   
   
2. How to select modules 
   use \[name of the module]
   
   
3. How to configure modules options & variables 
   ![[Pasted image 20230720105347.png]]
   - Lhost and Lport are usefull when a module provides us a reverse shell. (1) exploit is sent and executed on target system (2) payload initiates reverse connection to the attacker (for this the Lhost and Lport are needed). 

show options 
   
4. How to search for payloads 
   
   search -h
   ![[Pasted image 20230720112145.png]]
   search eternalblue 
   
5. Managing sessions 
   
   command view active sessions: sessions 
   
6. Additional functionality 
   ![[Pasted image 20230720112915.png]]
   command allows banner  : connect 
   
7. Saving your configurations 


used to set the global variable value for the RHOSTS option -> setg RHOSTS 
query can be used to limit the results to only display modules compatible with Windows --> search type:exploit platform:windows 

##### Workspaces 

Workspaces allow you to keep track of all your hosts, scans and activities and are extremely useful when conducting penetration tests as they allow you to sort and organize data based on the target or organization 

MSFconsole provides you with the ability to create, manage and switch between multiple workspaces depending on your requirements 

check status of dc - command: db_status 
check help menu workspaces - command: workspace -h 
check which workspace you in - command: workspace 

create new workspace - command: workspace -a Test 
command: hosts 
switch back to another workspace - command: workspace \[name workspace]
delete workspace - command: workspace -d \[name workspace]
rename workspace - command: workspace -r \[workspace name] \[new name workspace]



