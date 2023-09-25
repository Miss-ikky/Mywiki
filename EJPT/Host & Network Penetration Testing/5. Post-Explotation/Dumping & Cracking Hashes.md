

## Windows Hashes




##### NTLM hashes 










- WIndows OS stores hashed user account passwords in SAM database (locally)
	- when you enter cleartext password - it is hashed - compared to hash stored 
	- SAM database cannot be copied when the operating system is running 
	- The Windows NT kernel keeps the SAM database file locked and as a result, attackers typically utilize in-memory techniques and tools to dump SAM hashes from the LSASS process 
	- Elevated/Admin privileges are required in order to access and interact with the LSASSS process. 
	- In modern versions of Windows, the SAM databases is encrypted with a syskey
- Authentication and verification of user credentials is facilitated by the local security authority (LSA)
- Windows versions up to Windows server 2003 utilize two different types of hashes: 
	- LM (disabled)
	- NTLM (from windows vista on..)
		- Authentication between computers. 
		- MD5 hash 
![[Pasted image 20230925191444.png]]

- We can dump windows password hashes by using: 
	- Inbuilt meterpreter "hashdump" command 
	- Mimikatz 
- After dumping the hashes we can crack them through the use of:
	- John The Ripper 
	- Hashcat 


- find vulnerability and exploit it 
- migrate to ``lsass`` (upgrade meterpreter session and stablize)
- check if you need to elevate priviliges
- ``hashdump``
- ``shell`` 
	- net user (check if you have all the hashes of the users on system)
terminate shell 

New terminal: 
- vim hashes.txt - paste the hashes 
- cat hashes.txt 

how to crack ntlm hashes with John the ripper? 
- ``john to see manual ``
- ``john --list=formats ``
- `` john --list=formats | grep NT ``
- ``john --list=NT file_that_contains_hashes wordlist ``
  (if you do not specify wordlist than john will use default wordlist)
``gzip -d /usr/share/wordlists/rockyou.txt.gz  ``
the rockyou wordlist is always zipped. unzip before using it 
``john --list=NT file_that_contains_hashes wordlist=/usr/share/wordlists/rockyou.txt ``


use the cracked hashes to log in with psexec or ssh/rdp 












## Linux hashes 

##### Linux Password Hashes 


