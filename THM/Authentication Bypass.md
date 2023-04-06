
**Username Enumeration**
-    
- ![[Pasted image 20230406153613.png]]
	- , the `-w` argument selects the file's location on the computer that contains the list of usernames that we're going to check exists. 
	- The `-X` argument specifies the request method, this will be a GET request by default, but it is a POST request in our example.
	- The `-d` argument specifies the data that we are going to send. In our example, we have the fields username, email, password and cpassword. 
	- We've set the value of the username to **FUZZ**. In the ffuf tool, the FUZZ keyword signifies where the contents from our wordlist will be inserted in the request. 
	- The `-H` argument is used for adding additional headers to the request. In this instance, we're setting the `Content-Type` to the webserver knows we are sending form data. 
	- The `-u` argument specifies the URL we are making the request to.
	- The `-mr` argument is the text on the page we are looking for to validate we've found a valid username.
- ..