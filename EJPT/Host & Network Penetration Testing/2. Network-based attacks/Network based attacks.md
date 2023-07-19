- network-based attack
	- not related to operating system but attacks on network 
		- arp 
		- dhcp
		- smb 
		- ftp
		- telnet 
		- ssh 
- man in the middle attack  
![[Pasted image 20230718125000.png]]


###  Tshark 
**Tshark** is a command line tool created by the Wireshark team and shares the same powerful parsing engine as Wireshark. It is capable of doing most things we've come to love Wireshark for, but with the "from command line" advantage. This makes it ideal for batch analysis, offline processing and routine automation of traffic analysis tasks. In this course, we will explore many of these capabilities. It is assumed you have a basic working knowledge of Wireshark and traffic analysis.

check what version we have 
- command: tshark -v 
we want to see the manual but only one page at the time 
- command: tshark -man | more 
get a list of interfaces we can interact with 
- command: tshark -D 
select the eth interface 
- command: tshark -i eth0 
when you have a pcap 
- tshark -r pcapfilename 
run statistics on pcap 
- command: tshark -r pcap -z io,phs -q 


-- Lab --- 
**Set A:**
1. Which version of Tshark is installed in the lab? 
   
   student@attackdefense:~$ tshark -v
TShark (Wireshark) 2.6.1 (Git v2.6.1 packaged as 2.6.1-0ubuntu2~16.04.0)
   
2. Find all Tshark supported network interfaces for monitoring
student@attackdefense:~$ tshark -D
1. eth0
2. any
3. lo (Loopback)
4. ip_vti0
5. nflog
6. nfqueu
3. What is the Tshark command to sniff on eth0? Why did this command fail?
student@attackdefense:~$ tshark -i eth0
Capturing on 'eth0'
tshark: The capture session could not be initiated on interface 'eth0' (You don't have permission to capture on that device).
Please check to make sure you have sufficient permissions, and that you have the proper interface or pipe specified.0 packets captured

**Set B:**

1. Tshark supports PCAP files. The lab environment has a sample file: HTTP_traffic.pcap. How can you read this file in Tshark and display the packet list on the console?
   
   tshark -r pcapfilename 
   
2. How can you find the total number of packets in HTTP_traffic.pcap?
   
   student@attackdefense:~$ tshark -r  HTTP_traffic.pcap | wc -l 
  30418  
  
1. Tshark command to read the first 100 packets only from HTTP_traffic.pcap?
   
tshark -r HTTP_traffic.pcap -c 100


4. Print the list of protocols in HTTP_traffic.pcap 
![[Pasted image 20230718141506.png]]


### Filtering Basics 

apply a filter that will look at http
- command: tshark -r HTTP_traffic.pcap -Y 'http'
- command:  tshark -r HTTP_traffic.pcap -Y 'http.request.method\==GET'
- command: tshark -r HTTP_traffic.pcap -Y 'http.request.method\==GET
   -Tfields -e frame.time -e ip.src -e ttp.request.full_uri' | more 
- command:  tshark -r HTTP_traffic.pcap -Y 'http contains password'
- command:  tshark -r HTTP_traffic.pcap -Y 'http.request.method\==GET && http.host\==www\.2nos.com' -Tfield -e ip.dst 
lets look at traffic between two IP addresses 
- command: tshark -r HTTP_traffic.pcap -Y 'ip.src\==ip && ip.dst\==52.XX'  
traffic between ip 
- command: tshark -r HTTP_traffic.pcap -Y 'ip contains amazon.in && ip.src\==192.x.x.x.' -Tfields -e ip.src -e http.cookie 
see which browser we are seeing 
- command:  tshark -r HTTP_traffic.pcap -Y 'ip.src\==192.x.x.x && http' -Tfields -e http.user_agent 

-- lab --- 

Set A:
1. Command to show only the HTTP traffic from a PCAP file? 
   
   tshark -r HTTP_traffic.pcap -Y 'http' 
2. Command to show only the IP packets sent from IP address 192.168.252.128 to IP address 52.32.74.91?
   
   tshark -r HTTP_traffic.pcap -Y 'ip.src\==192.168.252.128 && ip.adr\==52.32.74.91'
3. Command to print only packets containing GET requests?
   tshark -r HTTP_traffic.pcap -Y 'http.request.method\==GET'
   
4. Command to print only packets only source IP and URL for all GET request packets?
   
tshark -r HTTP_traffic.pcap -Y 'http.request.method\==GET
   -Tfields -e frame.time -e ip.src -e http.request.full_uri'

Set B:
1. How many HTTP packets contain the "password" string? 4 
    tshark -r HTTP_traffic.pcap -Y 'http contains password' 
   
   
2. . What is the destination IP address for GET requests sent for New York Times (www.nytimes.com)?
   tshark -r HTTP_traffic.pcap -Y 'http.request.method\==GET && http.host\==www.nytimes.com' -Tfields -e ip.dst 

1. What is the session ID being used by 192.168.252.128 for Amazon India store (amazon.in)?
   
 tshark -r HTTP_traffic.pcap -Y 'ip contains amazon.in && ip.src\==192.168.252.128' -Tfields -e ip.src -e http.cookie 

2082758401l 

1. What type of OS the machine on IP address 192.168.252.128 is using (i.e. Windows/Linux/MacOS/Solaris/Unix/BSD)? Bonus: Can you also guess the distribution/flavor?

192.168.252.128 Mozilla/5.0 (X11; Linux x86_64; rv:31.0) Gecko/20100101 Firefox/31.0 Iceweasel/31.8.0 


### Arp Poisoning 


Run an nmap on the target subnet 
open wireshark on eth1
ping one target ip
- command: echo 1 > /proc/sys/net/ipv4/ip_forward 
lets start with arpspoof - "ip1" with the IP address of the target you want to intercept or spoof, and "ip2" with the IP address of the host you want to redirect the traffic to. 
- command: arpspoof -i eth1 -t ip1 -r ip2 

telnet is not encrypted so look for those credentials and use it to login 

-- Lab -- 

Try to intercept network traffic and steal `telnet` credentials by performing an ARP poisoning attack. 

ine login: admin
.
admin

Password: MyS3cr3tP455
.

Nmap scan report for 10.100.13.36
Host is up (0.00049s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE
22/tcp open  ssh
23/tcp open  telnet
MAC Address: 08:00:27:4A:45:F3 (Oracle VirtualBox virtual NIC)


### WiFi Traffic Analysis 

- Protocol 802.11 = wifi 
- ![[Pasted image 20230719121414.png]]
- 

-- Lab --- 

A WiFi traffic capture is provided in the lab. Analyze the traffic using Wireshark and answer the following questions:

1. What is the name of the Open (No Security) SSID present in the packet dump?  
   SSID (Service Set Identifier) is a unique name that identifies a wireless network. When a device, such as a laptop or smartphone, wants to connect to a Wi-Fi network, it searches for available SSIDs in its vicinity 
   
   SSID: SecurityTube_Open
   
2. The SSID 'Home_Network' is operating on which channel?
   
   
3. Which security mechanism is configured for SSID 'LazyArtists'? Your options are: OPEN, WPA-PSK, WPA2-PSK.
   
   
   
4. Is WiFi Protected Setup (WPS) enabled on SSID 'Amazon Wood'?  State Yes or No.
   
   
5. What is the total count of packets which were either transmitted or received by the device with MAC e8:de:27:16:87:18?
   
   
   
6. What is the MAC address of the station which exchanged data packets with SSID 'SecurityTube_Open'?
   
   
   
7. From the last question, we know that a station was connected to SSID 'SecurityTube_Open'. Provide TSF timestamp of the association response sent from the access point to this station
