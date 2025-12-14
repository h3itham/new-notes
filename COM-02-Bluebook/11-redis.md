1. mkdir for redis data and redis configuration 
	```bash 
	# Create main Redis directory
	mkdir -p ~/redis
	# Create config file
	touch ~/redis/redis.conf
	# Create data directory
	mkdir -p ~/redis/data
	```
2. create redis coniguration 
	```txt 
	# vim ~/redis/redis.conf
	bind 0.0.0.0
	appendonly yes
	appendfsync everysec
	#save 2 1
	maxmemory-policy noeviction
	
	# Redis password authentication
	requirepass Bluebook@2024
	```
1. Create Container 
	```bash 
	docker run -d \
	  --name redis \
	  --network bluebook \
	  --ip 192.168.1.183 \
	  -p 6379:6379 \
	  -v ~/redis/data:/data \
	  -v ~/redis/redis.conf:/usr/local/etc/redis/redis.conf \
	  --restart always \
	  redis:7 \
	  redis-server /usr/local/etc/redis/redis.conf
	```