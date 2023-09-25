

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

















## Linux hashes 

##### Linux Password Hashes 


