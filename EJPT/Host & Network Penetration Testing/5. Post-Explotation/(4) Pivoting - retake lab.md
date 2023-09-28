
From one system get access to other systems within the same network 
Meterpreter provides us with the ability to add a network route to the internal networks subnet performing **port-forwarding** and scan and exploit other systems on the network. 


Port Forwarding 
- Process of redirecting traffic from a specific port on a target system to a specific port on our system 
- Forward a remote port on a previously inaccessible host to a local port on our Kali Linux system so that we can remotely interact/exploit the service running on the port

![[Pasted image 20230925204807.png]]
We can access victim one bc we exploited it and now have access to it. 
With networking information (enumeration) we know that victim 1 is part of a internal/private subnet. 
We can use victim 1 as a foothold to get access to victim 2
We cannot access victim 2 through our kali system, we need to use victim 1 that we compromised to get access to victim 2. 
We need to know what ports are open on victim2 so we can try to exploit the services - to find this out we need port-forwarding. 


---


1. ``ping`` victim 1 
2. ``ping`` victim 2 (no response means we cannot connect, only through victim 1 )
3. nmap service detection on victim 1 
4. exploit victim 1 
5. Perform local enumeration on victim 1 
	1. ``sysinfo`` 
	2. ``getuid`` 
	3. ``ipconfig`` (the netmask gives id of the range)
6. add route from victim 1 to victim 2 by adding entire subnet that victim 1 is part of: ``run autoroute -s 10.10.29.0/20``![[Pasted image 20230925210153.png]]
7. ``run autoroute -p`` to display routing table 
	when the subnet is added we can scan any device on that subnet, from within msfconsole 
8. search `portscan` (use portscan/tcp) (the scanning is done through victim 2 bc we cannot communicate directly to victim 2)
	1. ``show options`` 
	2. specify ``target ip`` (victim 2 machine) 
	3. set ``port range ``

Next step is to identify exact service version on a port (e.g. 80) on victim 2 - the best tool for this is nmap. Nmap cannot be run in msfconsole therefore, port-forwarding is needed. Get back into meterpreter session of victim machine 1: 
- ``portfwd add -l 1234 -p 80 -r victim2_ip ``
  (= add following local port 1234 and we are forwarding port 80 on victim machine 2 to kali linux system) We will be able to access the service on port 80 on victim 2.
- open new tab
- ``nmap -sV -p 1234 localhost`` 
  (identify the exact service that is running on port 80 on victim_machine_2)
- when you identified vulnerable services on victim 2. Go back to msfconsole. and try to exploit it: 
	- search exploit module of vulnerable service on victim 2
	- set ``payload`` (not a reverse tcp but a bind tcp) ``windows/meterpreter/bind_tcp`` 
	- ``set rhost victim2_machineip``
	- run 
	meterpreter session on victim 2 



