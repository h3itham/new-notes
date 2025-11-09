1. Install logrotate 
	```bash 
	sudo apt update
	sudo apt install logrotate
	```
2. Create the logrotate configuration file
	```bash 
	sudo nano /etc/logrotate.d/docker-containers
	```
	and Add the following 
	```txt 
	/var/lib/docker/containers/*/*.log {
	    daily
	    rotate 7
	    compress
	    delaycompress
	    missingok
	    notifempty
	    dateext
	    dateformat -%Y%m%d
	    copytruncate
	    create 0644 root root
	    su root root
	    maxsize 100M
	    sharedscripts
	    postrotate
	        /usr/bin/docker system prune -f --volumes >/dev/null 2>&1 || true
	    endscript
	}
	```
3. Verify your configuration without actually rotating any logs
	```bash 
	sudo logrotate -d /etc/logrotate.d/docker-containers
	```
4. Force Rotation imdiatlly 
	```bash 
	sudo logrotate -f /etc/logrotate.d/docker-containers
	```

