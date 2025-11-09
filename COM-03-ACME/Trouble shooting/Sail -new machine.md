
- The main problem with sail that there is no php installed on it use the following commands to install it 
  ```bash
    apt-get remove --purge -y php8.3*
	apt-get autoclean && apt-get autoremove
	rm -rf /etc/php/8.3
	rm -rf /etc/alternatives/php*
	
	# Add PHP repository again
	add-apt-repository ppa:ondrej/php
	apt-get update
	
	# Install PHP fresh
	apt-get install -y php8.3-cli php8.3-dev php8.3-mongodb
	```
- There is anther problem whic related to mysql it dose not installed on it 
  ```bash
	   apt-get install -y php8.3-mysql
	```

- Load all env file 
  ```bash
    export $(grep -v '^#' .env | xargs)
	```