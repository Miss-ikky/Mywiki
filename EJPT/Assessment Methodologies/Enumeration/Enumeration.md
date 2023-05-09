

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
| rdp-ntlm-info:
|   Target_Name: WIN-OMCNBKR66MN
|   NetBIOS_Domain_Name: WIN-OMCNBKR66MN
|   NetBIOS_Computer_Name: WIN-OMCNBKR66MN
|   DNS_Domain_Name: WIN-OMCNBKR66MN
|   DNS_Computer_Name: WIN-OMCNBKR66MN
|   Product_Version: 6.3.9600
|_  System_Time: 2023-05-09T09:37:12+00:00
| ssl-cert: Subject: commonName=WIN-OMCNBKR66MN
| Not valid before: 2023-05-08T08:54:07
|Not valid after:  2023-11-07T08:54:07
49152/tcp open  msrpc              Microsoft Windows RPC
49153/tcp open  msrpc              Microsoft Windows RPC
49154/tcp open  msrpc              Microsoft Windows RPC
49155/tcp open  msrpc              Microsoft Windows RPC
49165/tcp open  msrpc              Microsoft Windows RPC
MAC Address: 02:A3:CA:EC:C9:AE (Unknown)
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:window

Host script results:
nbstat: NetBIOS name: WIN-OMCNBKR66MN, NetBIOS user: <unknown>, NetBIOS MAC: 02:a3:ca

smb-os-discovery:

smb-security-mode:

smb2-security-mode:














