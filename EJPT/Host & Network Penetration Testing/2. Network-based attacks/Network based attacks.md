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
-  `tshark -v `
we want to see the manual but only one page at the time 
- `tshark -man | more `
get a list of interfaces we can interact with 
- :` tshark -D `
select the eth interface 
- command: `tshark -i eth0 `
when you have a pcap 
- `tshark -r pcapfilename `
run statistics on pcap 
-  `tshark -r pcap -z io,phs -q `



### Filtering Basics 

apply a filter that will look at http
-  `tshark -r HTTP_traffic.pcap -Y 'http'`
-  ` tshark -r HTTP_traffic.pcap -Y 'http.request.method\==GET'`
-  `tshark -r HTTP_traffic.pcap -Y 'http.request.method\==GET`
`   -Tfields -e frame.time -e ip.src -e ttp.request.full_uri' | more` 
-   `tshark -r HTTP_traffic.pcap -Y 'http contains password'`
-   `tshark -r HTTP_traffic.pcap -Y 'http.request.method\==GET && http.host\==www\.2nos.com' -Tfield -e ip.dst `
lets look at traffic between two IP addresses 
-  `tshark -r HTTP_traffic.pcap -Y 'ip.src\==ip && ip.dst\==52.XX'  
traffic between ip `
-  `tshark -r HTTP_traffic.pcap -Y 'ip contains amazon.in && ip.src\==192.x.x.x.' -Tfields -e ip.src -e http.cookie `
see which browser we are seeing 
-  `tshark -r HTTP_traffic.pcap -Y 'ip.src\==192.x.x.x && http' -Tfields -e http.user_agent `


### Arp Poisoning 


Run an nmap on the target subnet 
open wireshark on eth1
ping one target ip
- command: `echo 1 > /proc/sys/net/ipv4/ip_forward `
lets start with `arpspoof - "ip1"` with the IP address of the target you want to intercept or spoof, and "ip2" with the IP address of the host you want to redirect the traffic to. 
- command: `arpspoof -i eth1 -t ip1 -r ip2 `

telnet is not encrypted so look for those credentials and use it to login 

### WiFi Traffic Analysis 

- Protocol 802.11 = wifi 
- ![[Pasted image 20230719121414.png]]

