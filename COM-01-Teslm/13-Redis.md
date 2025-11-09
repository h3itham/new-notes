#### Redis as a container 
- Create volume
	```bash
	docker volume create redis_data
	```
- Create container 
	```bash
	docker run -d \
	  --name redis-container \
	  -p 6379:6379 \
	  -v redis_data:/data \
	  redis:7 \
	  redis-server --bind 0.0.0.0
		
	```
- with presistence volume ( Snapshot + OAF ) 
	```bash
	docker run -d \
	  --name redis-container \
	  -p 6379:6379 \
	  -v redis_data:/data \
	  redis:7 \
	  redis-server --bind 0.0.0.0 \
	  --save 60 1 --save 300 10 --save 900 1 \
	  --appendonly yes --appendfsync everysec
	```	

#### Redis as a service 
1. install redis service
	```bash 
	 sudo apt update 
	 sudo apt install redis-server
	```
2. bind it to listen to any IP 
	```bash 
	sudo vim /etc/redis/redis.conf 
	# Bind 
	# protected-mode no
	```
3. Restart redis 
	```bash 
	sudo systemctl restart redis.service
	```