
**Getting started:**

-   Kernel: allows hardware to talk to software
-   Linux system has 3 parts:
    -   Hardware
    -   Linux kernel (core of OS, manages hardware and tells how to interact with the system)
    -   User space (users interact directly with system)

-   Shell: program that takes your commands from the keyboard and sends them to the operating system to perform.
    
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


| Command | use                                                                                                                                                                     | Uitleg                                                                                                                                                                                                                                                                                                                             |
| ------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| echo    | echo                                                                                                                                                                    | prints out the text arguments to the display                                                                                                                                                                                                                                                                                       |
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
| exit    | exit or logout                                                                                                                                                          | exit the shell                                                                                                                                                                                                                                                                                                                                    |


Text manipulation and navigation 

| echo Hello World > peanuts.txt | send output to a file                                                                      |
| ------------------------------ | ------------------------------------------------------------------------------------------ |
| >                              | change where the standard output goes                                                      |
| >>                             | if the file exist it will apped to the file and if it doesnt exist it will create the file |
| echo                           | takes input from the keyboard and returns to the screen                                    |

