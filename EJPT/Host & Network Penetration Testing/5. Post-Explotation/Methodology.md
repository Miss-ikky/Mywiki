















- Post-exploitation is final phase of penetration testing process and consists of the tactics techniques and procedures that attackers/adversaries undertake after obtaining initial access on a target system. 
- Post-exploitation involves what you do or have to do once you gain initial foothold on the target system
- Post-exploitation will differ based on the target operating system as well as the target infrastructure

- The post-exploitation techniques and tools that you can use depend on what kind of access you have on the system you have compromised as well as ow stealthy you have to be
- This ultimately means that you need to utilize different techniques and tools based on the target operating system and its configuration 
- the post-exploitation techniques you can run against the target will need to abide by the rules of engagement agreed upon with the client you are performing the pentest for. 

![[Pasted image 20230917162752.png]]

##### Post-Exploitation Methodology 

- Use structured methodology , this ensures that you do not skip or overlook important phases of post-exploitation phase.

![[Pasted image 20230917163519.png]]

phase 1: local enumeration of the target/ get a the info you can find
phase 2: transfer file to target system after getting access
phase 3: if you obtained a non interactive shell or standard command shell you need to upgrade to meterpreter session to preform additional task
phase 4: if you have access to a target system without elevated priviliges yo need to elevate your privileges. 
phase 5: set up persistent access to target, incase system is shutdown you can access the target again without exploiting the vulnerability again. 
phase 6: dump password hashes and utilize these for legitimate authentication. 
phase 7: identifying target systems on the target network that you can compromise and exploiting them and move on to next system in network.
phase 8: clear task 

Steps per phase 

![[Pasted image 20230917164125.png]]

![[Pasted image 20230917164150.png]]
![[Pasted image 20230917164212.png]]

![[Pasted image 20230917164223.png]]

![[Pasted image 20230917164238.png]]

![[Pasted image 20230917164248.png]]

![[Pasted image 20230917164307.png]]

![[Pasted image 20230917164325.png]]


