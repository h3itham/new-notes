
#### Introduction 
- FPM stands for FastCGI Process manager. 
- Critical component for improving php performance for webservers. 
- In General any process will be ideal until the request. Then it will start 
  ![](fpm.png)
- Default location for configuration file
  ```bash 
    vim /usr/local/etc/php-fpm.d/www.conf
	```

- This is my current configurtion 
	- Basic Configuration 
	  ```txt
	     [www]  
	     user = www-data
	     group = www-data 
	     listen = 9000
		```
		- www name of process. 
	
	- Process manager configuration 
	  ```txt 
	    pm = dynamic 
	    pm.max_children = 10 
	    pm.start_servers = 4 
	    pm.min_spare_servers = 2 
	    pm.max_spare_servers = 5 
	    pm.max_requests = 700 
		```

		- pm = dynamic this for mode. 
		- pm.start_servers for the number of process that start at the begining. 
		- pm.max_requests This is the maximum number of requests child process can handle before respawning 


- Restart FPM gracefull 
  ```bash 
    kill -USR2 1 
	```
	