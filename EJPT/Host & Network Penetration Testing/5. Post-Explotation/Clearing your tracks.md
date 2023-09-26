
#### Windows 

- The exploitations and post-exploitation faces of a penetration test involves actively engaging with target systems and the data that is stored on these systems 
- As a result you may be required to clear any changes you have made to the target systems you have compromised based on the guidelines specified in the rules of engagement 
- if you have transferred any files to the target systems you have compromised, keep track of where they have been saved so that you can remove then when done 
- A good practice is to store all scripts, exploits and binaries in the C:/Temp directory on Windows and the /tmp directory on Linux

also, 

- It is important to consider the exploitation framework when you are using MSF, which is known for generating and storing artifacts on the target system 
- Some well designed MSF modules provide you with instructions and resource scripts that provide you with information regarding where the artifacts are stored and how they can be removed. 
- In the connect of Windows, a typical post-exploitation technique pertinent to clearing your tracks is to delete the Windows Event Log. ( do not do this with penetration test)

moreover, 

- with msfmodules type show into to see what the module does. 
![[Pasted image 20230925214145.png]]
- upload exploits to temp directory in windows and linux in tmp 
- make sure to remove the files with resource scripts provided: Cleanup Meterpreter RC file
	![[Pasted image 20230925214935.png]]
	cat resource script to check the automations of deleting the files 


Methods to clear tracks after exploiting victim: 

- Resource scripts: 
	- In meterpreter of the target run the resource file: ``resource path/resource_file.rc `` ![[Pasted image 20230925215156.png]]
	- check temp file to doublecheck if you deleted everything 




#### Linux 

keep an eye of resource scripts and where they are stored, so you can use them and run it on target system to undo what the exploitation module did. 

- when transferring file always upload to centralized directory -> /tmp 
- Every user has a bash_history file: ``history`` command gives overview of commands used. One way to clean up is by deleting the commands you typed instead of deleting the content of bash history file, this makes it to obvious. 
		- clear bash history: ``hisctory -c ``




