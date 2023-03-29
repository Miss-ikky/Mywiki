
Getting started 
-   Kernel: allows hardware to talk to software
-   Linux system has 3 parts:
    -   Hardware
    -   Linux kernel (core of OS, manages hardware and tells how to interact with the system)
    -   User space (users interact directly with system)

- Linux basics
	- Everything is a file 
	- Configuration data stored in a text file 
	- *Bootloader*: A piece of code that runs to guide the booting process to start the operating system. Parrot Linux uses the GRUB Bootloader. 
	- *OS Kernel*:  The kernel is the **main** component of an operating system. It manages the resources for system's I/O devices at the hardware level. 
	- *Deamons*: Background services are called "**daemons**" in Linux. Their purpose is to ensure that key functions such as scheduling, printing, and multimedia are working correctly. These small programs load after we booted or log into the computer. 
	- *OS Shell*: The operating system shell or the command language interpreter (also known as the command line) is the interface between the OS and the user. This interface allows the user to tell the OS what to do. The most commonly used shells are Bash, Tcsh/Csh, Ksh, Zsh, and Fish. 
	- *Graphics server*: This provides a graphical sub-system (server) called "X" or "X-server" that allows graphical programs to run locally or remotely on the X-windowing system. 
	- *Window Manager*: Also known as a graphical user interface (GUI). There are many options, including GNOME, KDE, MATE, Unity, and Cinnamon. A desktop environment usually has several applications, including file and web browsers. These allow the user to access and manage the essential and frequently accessed features and services of an operating system. 
	- *Utilities*: Applications or utilities are programs that perform particular functions for the user or another program. 

- Linux Architecture 
	- *Hardware*:  Peripheral devices such as the system's RAM, hard drive, CPU, and others.
	- *Kernel*:  The core of the Linux operating system whose function is to virtualize and control common computer hardware resources like CPU, allocated memory, accessed data, and others. The kernel gives each process its own virtual resources and prevents/mitigates conflicts between different processes.
	- *Shell*:  A command-line interface (**CLI**), also known as a shell that a user can enter commands into to execute the kernel's functions.
	- *System Utility*: Makes available to the user all of the operating system's functionality.
	- *Shell*: program that takes your commands from the keyboard and sends them to the operating system to perform.

- File System Hierarchy 
	-  Filesystem Hierarchy 

![[Pasted image 20230329120031.png]]

- */*     Top-level directory is the root filsystem and contains all of the files required to boot the operating system before other filesystems are monuted as well as th efiles required to boot the other filesystems. After boot, all of the other filesystems are mounted at standard mount points as subdirectories of the root 
- */bin* Contains essential command binaries 
- */boot* Contists of the static bootloader, kernel executable, and files required to boot the Linux OS
- */dev* Contains device files to facilitate access to every hardware device attached to the system 
- */etc* Local system configuration files. Configuration files for installed applications may be saved here as well 
- */home* Each user on the system has a subdirectory here for storage 
- */lib* shared libarary files that are required for boot system 
- */media* External removable media devices such as USB drives are mounted here 
- */mnt* Temorary mount point for regular filesystem 
- */opt* Optional files such as third-party tools can be saved here 
- */root* The home directory for the root user 
- *sbin* This directory contains exectuable used for system administration 
- */tmp* The operating system and many programs use this directory to store temporary files. This directory is generally cleared upon system boot and may be deleted at other times without any warning 
- */usr* Contains executables , libraries, man files 
- */var* This directory contains variable data files such as log files, email in-boxes, web application, cron files and more 


- Linux Distributions (operating system based on the linux kernel) 
	-  Advanced Package Tool (apt) package management sytem to handle software updates and security patches 
	-  Package management system helps keep the system up-to-date and secure by automatically downloading and installing security updates as soon as they are available

- Introduction to Shell 
	- Linux terminal is a shell or command line,
	- Shell provides text-basde input/ouput interface between users and the krenel for a computer system. 
	- Most commonly used shell in Linux is the Bourne-Again Shell (BASH) (Zsh, FIsh, Ksh)
	

- Terminal Emulators
	- Software that emulates the function of a terminal. It allows the use of text-based programs within a graphical user interface (GUI) 
	- Command-line interfaces (CLI) that run as additional terminals in one terminal 
	- Terminal serves as an interface to the shell interpreter 


- Prompt Description 
	- Home directory for a user is tilde: ~ this is the default folder when you log in 
	- The dollar sign stands for user $ -> When you log in as *root*, the character changes to hash *#* 
	- The prompt can be customized using special characters and variables in the shell configuration file .bashrc for the Bash shell 
	- de character 
		-  *\\u*     current username  
		- *\\h*     hostname 
		- *\\w*  current working directory 
		- *\\d*   Date 
		- *\\D{%Y-%m-%d}*  Date 
		- *\\H* Full hostname 
		- *\\j* Number of jobs managed by the shell 
		- *\\n* Newline 
		- *\\r* Carriage return 
		- *\\s* Name of the shell 
		- *\\t* Current time 
		- *\\T* Current time 
		- *\\@* current time 

- Help 
	- man  {tool} 
	- man  {tool} --help
	- man {tool} -h 
	- apropos {keyword}


- System information 
	- *whoami*     Displays current username
	- *id*               Returns users identify / prints our effective group member ship IDs 
	- *hostname*  Sets or prints the name of current host system 
	- *host*           print the name of the computer that we are logged into 
	- *uname*       Prints basic information about the operating sytem name and system hardware
		- *uname -a* print all information about the machine in specifiek order: Kernel Name, Hostname, Kernel Release, Kernel Version, Machine hardware, Operating system 
		- *uname -r*  print kernel release (to find exploits)
	- *pwd*           Returns working directory name 
	- *ifconfig*      The ifconfig utility is used to assign or to view an address to a network interface or configure network interface parameters  
	- *ip*              IP is a utility to show or manipulate routing, netwokring device, interfaces and tunnels 
	- *netstat*   shows network status 
	- *ss*           Another utility to investigate sockets 
	- *ps*          Shows process status 
	- *who*       Display who is logged in 
	- *env*         Prints environment or sets and executes command 
	- *lsblk*       Lists block devices 
	- *lsusb*      Lists USB devices 
	- *lsof*       Lists opened files 
	- *lspci*      List PCI devices 

 Voorbeeld: 
	 adm group; means that users can read log files in /var/log and can potentially get access to sensitive information, membership and sudo group. 

Logging in via SSH 
	- Secure Shel (SSH) refers to a protocol that allows clients to access  and excute commands or actions on remote computers 




-   Alles in Linux is een file → hierarchical directory free.
    
    -   First directory is aptly = root directory
        
        ![[Pasted image 20230324105033.png]]
        
    -   Location of a file is a path
        
-   Two ways to specify paths:
    
    -   Absolute path: path from the root directory → / (root). /home/pete/Desktop = you are starting from root directory
        
    -   Relative path: path from where you are currently in filesystem.
        
        ![[Pasted image 20230324105227.png]]
-   commands have flags that add more functionality; flags can be added together
    
-   In linux filenames arent required to represent the contents of the file ⇒ funny.gif hoeft geen gif te zijn
    
-   if you copy a file over to a directory that has the same filename, the file will be overwritten with whatever you are copying over!!!
    
-   when files are deleted with RM you can get it back - when directory is write-protected it will be protected
  
- About echo $PATH: Let's say you manually download and install a package from the internet and put it in to a non standard directory and want to run that command, you type $ [coolcommand] and the prompt says command not found. Well that's silly you are looking at the binary in a folder and know it exists. What is happening is that $PATH variable doesn't check that directory for this binary so it's throwing an error.Let's say you had tons of binaries you wanted to run out of that directory, you can just modify the PATH variable to include that directory in your PATH environment variable.


| Command | use                                                                                                                                                                     | Uitleg                                                                                                                                                                                                                                                                                                                             |
| ------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| echo    | echo                                                                                                                                                                    | prints out the text arguments to the display     <br> echo $HOME (shows path to home directory)<br> echo $USER shows your usernaem <br> echo $PATH (shows list of paths that the system searches when it runs a command )                                                                                                          |
| pwd     | pwd                                                                                                                                                                     | if you want to know which directory you are in                                                                                                                                                                                                                                                                                     |
| cd      | cd  'folder' or cd 'path'                                                                                                                                               | move around in directories                                                                                                                                                                                                                                                                                                         |
| ls      | ls   ls 'path'  ls -a    ls -l   ls -la                                                                                                                                 | List directory contents; will list directories and files in the current directory by default Files that start with . are hidden but shown with -a flag. the -l flag shows detailed list of files (file permissions, number of links, owner name, owner group, file size, timestamp of last modification, and file/directory name.) |
| touch   | touch 'name of file'                                                                                                                                                    | create new empty file                                                                                                                                                                                                                                                                                                              |
| file    | file 'file name'                                                                                                                                                        | create new empty file                                                                                                                                                                                                                                                                                                              |
| cat     | cat 'file'                                                                                                                                                              | read the content of a file (short content mainly)                                                                                                                                                                                                                                                                                  |
| less    | less 'path/file'                                                                                                                                                        | text is displayed in a paged manner. De volgende keys gebruik voor navigation: q, g, G, h /search                                                                                                                                                                                                                                  |
| history |                                                                                                                                                                         | look into the histroy of the command you used                                                                                                                                                                                                                                                                                      |
| clear   |                                                                                                                                                                         | clear the commands on display                                                                                                                                                                                                                                                                                                      |
| cp      | cp 'file you want to copy' 'destination you want to copy it to '                                                                                                        | make copy of a file: * wildcard, ? one character, [ ] any character, -r recursively copy files and directories within directory -i prompt you before overwriting a file.                                                                                                                                                           |
| mv      | mv [oldfilename]  [newfilename] <br> mv [file] [new [directory path] <br> mv [file1] [file2] [location] mv -i [directory1] [directory2] mv -b [directory1] [directory2] | move files and rename them move more than 1 files -i flag can be used to prompt you before overwriting anything -b flag to create backup of file                                                                                                                                                                                   |
| mkdir   | mkdir [..] […] mkdir -p [path]                                                                                                                                          | Make directory; you can make multiple directories at same time. create sub directories with the -p flag (parent flag)                                                                                                                                                                                                              |
| rm      | rm -f [file] rm -i [file] rm -r [directory] rmdir [directory]                                                                                                           | remove files and directories -f: force option to remove all files (with write protect and not, without prompt) -i (prompt) -r (recursive to remove all files and sub directories when deleting a directory)                                                                                                                        |
| find    | find [firectory you searching in] -name [file you are looking for] find [location] -type d -name [file you looking for]                                                 | find files on system d = directory -name = search by name                                                                                                                                                                                                                                                                          |
| help    | help 'command' or 'command' --help                                                                                                                                      | meer informatie over een command vinden en de flags die je kunt gebruiken                                                                                                                                                                                                                                                          |
| man     | man 'command'                                                                                                                                                           | the manual of a command                                                                                                                                                                                                                                                                                                            |
| whatis  | whatis 'command'                                                                                                                                                        | discription of what a command does                                                                                                                                                                                                                                                                                                 |
| alias   | voorbeeld: alias foobar='ls -la' (met dit voorbeeld hoef je allen maar foobar in te typen ipv ls -la)                                                                   | the alias command will not save after reboot. For permenant alias you need to ad it to ~/. bashrc <br> unwanted alias can be removed with: unalias 'alias you want to remove'                                                                                                                                                      |
| exit    | exit or logout                                                                                                                                                          | exit the shell                                                                                                                                                                                                                                                                                                                     |
| env     |                                                                                                                                                                         | output information about the environment variables you currently have set                                                                                                                                                                                                                                                          |
| cut     |                                                                                                                                                                          | It extracts portions of text from a file.  <br> -f flag cuts text based off of fields, <br>                                                                                                                                                                                                                                                                                                                                  |
| paste         |                                                                                                                                                                         |                                                                                                                                                                                                                                                                                                                                    |


Text manipulation and navigation 

- In de basis heb je the standard input (stdin) wat vaak een keyboard is die zorgt voor de input en deze kan dan standard output en/of standard error generegeren. In linux hoeft de standard input niet alleen te komen van andere devices het kan ook een andere file zijn
- I?O stream that is called standard error, by defayly stder send its output to the screen aswell. (different stream than stdout) 
![[Pasted image 20230324152304.png]]
standard input = 0  
standard output = 1 
standerd error = 2 


|echo Hello World > peanuts.txt | send output to a file                                                                      |
| ------------------------------ | ------------------------------------------------------------------------------------------ |
| >                              | change where the standard output goes                                                      |
| >>                             | if the file exist it will apped to the file and if it doesnt exist it will create the file |
| echo                           | takes input from the keyboard and returns to the screen                                    |

| cat < peauts.xt > banana.txt | redirect output of cat peanuts to another file <br> the standard input is de peanuts file and wat de uitkomst is wordt ogpeslagen in banana file |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| <                            | standard in                                                                                                                                      |


| ls /fake/directory > peanuts.txt 2>&1 | send results of the comand to the file and the nit redirects stderr to the stout via 2>&1                                                            |
| ------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| 2>                                    | send standard error to file                                                                                                                          |
| 2>&1                                  | will send standard error to whatever the standerd outpout is pointing to. <br> if it is pointing to file than it will send standard error to a file. |
| &>                                    | short way to redirect both stdout and stderr to file                                                                                                                                                      |

{command} 2> /dev/null  ---> redirect output to dev/null which wil ldiscard any input  

- Pipe and tee 
	-  The pipe operator | 
	- Allows to get the standard outpu of a command and make the standard input of another process 
	- voorbeeld: ls | tree peanut.txt 

