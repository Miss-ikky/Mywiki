80/tcp    open  http               Microsoft IIS httpd 8.5
|_http-server-header: Microsoft-IIS/8.5
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-title: Site doesn't have a title (text/html).
135/tcp   open  msrpc              Microsoft Windows RPC
139/tcp   open  netbios-ssn        Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds       Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
3389/tcp  open  ssl/ms-wbt-server?
| ssl-cert: Subject: commonName=steelmountain
| Not valid before: 2023-09-29T11:23:29
|_Not valid after:  2024-03-30T11:23:29
| rdp-ntlm-info:                                                                                              
|   Target_Name: STEELMOUNTAIN                                                                                
|   NetBIOS_Domain_Name: STEELMOUNTAIN                                                                        
|   NetBIOS_Computer_Name: STEELMOUNTAIN                                                                      
|   DNS_Domain_Name: steelmountain                                                                            
|   DNS_Computer_Name: steelmountain                                                                          
|   Product_Version: 6.3.9600                                                                                 
|_  System_Time: 2023-09-30T11:28:19+00:00                                                                    
|_ssl-date: 2023-09-30T11:28:24+00:00; 0s from scanner time.                                                  
5985/tcp  open  http               Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)                                    
|_http-server-header: Microsoft-HTTPAPI/2.0                                                                   
|_http-title: Not Found                                                                                       
8080/tcp  open  http               HttpFileServer httpd 2.3                                                   
|_http-server-header: HFS 2.3                                                                                 
|_http-title: HFS /                                                                                           
47001/tcp open  http               Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)                                    
|_http-server-header: Microsoft-HTTPAPI/2.0                                                                   
|_http-title: Not Found                                                                                       
49152/tcp open  msrpc              Microsoft Windows RPC                                                      
49153/tcp open  msrpc              Microsoft Windows RPC                                                      
49154/tcp open  msrpc              Microsoft Windows RPC                                                      
49155/tcp open  msrpc              Microsoft Windows RPC                                                      
49156/tcp open  msrpc              Microsoft Windows RPC                                                      
49168/tcp open  msrpc              Microsoft Windows RPC                                                      
49169/tcp open  msrpc              Microsoft Windows RPC                                                      
MAC Address: 02:CC:2E:40:DC:79 (Unknown)                                                                      
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).           
TCP/IP fingerprint:
OS:SCAN(V=7.93%E=4%D=9/30%OT=80%CT=1%CU=42999%PV=Y%DS=1%DC=D%G=Y%M=02CC2E%T
OS:M=65180658%P=x86_64-pc-linux-gnu)SEQ(SP=105%GCD=1%ISR=105%TI=I%CI=I%II=I
OS:%SS=S%TS=7)OPS(O1=M2301NW8ST11%O2=M2301NW8ST11%O3=M2301NW8NNT11%O4=M2301
OS:NW8ST11%O5=M2301NW8ST11%O6=M2301ST11)WIN(W1=2000%W2=2000%W3=2000%W4=2000
OS:%W5=2000%W6=2000)ECN(R=Y%DF=Y%T=80%W=2000%O=M2301NW8NNS%CC=Y%Q=)T1(R=Y%D
OS:F=Y%T=80%S=O%A=S+%F=AS%RD=0%Q=)T2(R=Y%DF=Y%T=80%W=0%S=Z%A=S%F=AR%O=%RD=0
OS:%Q=)T3(R=Y%DF=Y%T=80%W=0%S=Z%A=O%F=AR%O=%RD=0%Q=)T4(R=Y%DF=Y%T=80%W=0%S=
OS:A%A=O%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=80%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=
OS:Y%DF=Y%T=80%W=0%S=A%A=O%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=80%W=0%S=Z%A=S+%F=A
OS:R%O=%RD=0%Q=)U1(R=Y%DF=N%T=80%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%R
OS:UD=G)IE(R=Y%DFI=N%T=80%CD=Z)

Network Distance: 1 hop
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
|_nbstat: NetBIOS name: STEELMOUNTAIN, NetBIOS user: <unknown>, NetBIOS MAC: 02cc2e40dc79 (unknown)
| smb-security-mode: 
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   302: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2023-09-30T11:28:19
|_  start_date: 2023-09-30T11:23:21

