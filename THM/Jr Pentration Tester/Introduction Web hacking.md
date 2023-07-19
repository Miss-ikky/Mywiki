
**Content Discovery**  
-   Content Discovery: When we talk about content discovery, we're not talking about the obvious things we can see on a website; it's the things that aren't immediately presented to us and that weren't always intended for public access.  This content could be, for example, pages or portals intended for staff usage, older versions of the website, backup files, configuration files, administration panels, etc. 
-  Waar kunnen we zoeken? 
	- Manual Discovery 
		-  Robot.txt: doument that tells search engines which pages they are/arent allowed to show on their search engine results or ban from crawling. These pages may be areas such as administration portals or files meant for the website's customers. This file gives us a great list of locations on the website that the owners don't want us to discover as penetration testers. 
		- Favicon: Sometimes when frameworks are used to build a website, a favicon that is part of the installation gets leftover, and if the website developer doesn't replace this with a custom one, this can give us a clue on what framework is in use. OWASP host a database of common framework icons that you can use to check against the targets favicon [https://wiki.owasp.org/index.php/OWASP_favicon_database](https://wiki.owasp.org/index.php/OWASP_favicon_database). Once we know the framework stack, we can use external resources to discover more about it (see next section). ![[Pasted image 20230405120129.png]]
			- Usefull command:   curl {link to falvicon  to download it} | md5sum  
			- Use the md5sum to check the framework used 
		- Sitemap.xml file gives a list of every file the website owner whishes to be on the search engine -> visit url/sitemap.xml 
		- HTTP Headers can contain usefull information such as webserver software and programming/scripting language. 
			- Use curl command with -v (verbose mode) 
		- Framework stack -  learn more about the software and other information, possibly leading to more content we can discover.

**Osint** 
- Google hacking / Dorking utilizes Google's advanced search engine features, which allow you to pick out custom content ![[Pasted image 20230406120802.png]]
- Wappalyzer helps identify what technologies a website uses, such as frameworks, Content Management Systems (CMS), payment processors and much more, and it can even find version numbers as well. 
- Wayback machine ([https://archive.org/web/](https://archive.org/web/)) historical archive of websites
- Github - Git is a **version control system** that tracks changes to files in a project. GitHub is a hosted version of Git on the internet. Repositories can either be set to public or private and have various access controls.
- S3 Buckets storage service provided by Amazon AWS, allowing people to  save files and even static website content in the cloud accessible over HTTP and HTTPS.The owner of the files can set access permissions to either make files public, private and even writable. Sometimes these access permissions are incorrectly set and inadvertently allow access to files that shouldn't be available to the public.  
	- The format of the S3 buckets is http(s)://**{name}.**[**s3.amazonaws.com**](http://s3.amazonaws.com/) where {name} is decided by the owner, such as [tryhackme-assets.s3.amazonaws.com](http://tryhackme-assets.s3.amazonaws.com). S3 buckets can be discovered in many ways, such as finding the URLs in the website's page source, GitHub repositories, or even automating the process. One common automation method is by using the company name followed by common terms such as **{name}**-assets, **{name}**-www, **{name}**-public, **{name}**-private, etc.

**Automatied discovery** (using tools to discover content instead of manually)
- Wordlist  (text files that contain a long list of commonly used words)
	- [https://github.com/danielmiessler/SecLists](https://github.com/danielmiessler/SecLists) which Daniel Miessler curates. 
- Tools e.g. ffuf, dirb and gobuster 

