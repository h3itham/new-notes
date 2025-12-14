#### Redis as a container 
- Create volume
	```bash
	docker volume create redis_data
	```
	- Volume location in `hostinger` `/var/lib/containers/storage/volumes/redis_data/_data`
- Redis configuration 
	```bash 
	bind 0.0.0.0
	appendonly no 
	save 2 1
	```
- Create container 
	```bash 
	docker run -d \
	  --name redis-container \
	  -p 6379:6379 \
	  -v redis_data:/data \
	  -v /root/redis/redis.conf:/usr/local/etc/redis/redis.conf \
	  redis:7 \
	  redis-server /usr/local/etc/redis/redis.conf
	```
- Connect to Redis insight 
- Create a Key. 


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
3. Configure presentence 
	```txt 
	# --- RDB Snapshot ---
	save 900 1
	save 300 10
	save 60 10000
	# --- Append Only File (AOF) ---
	appendonly yes
	appendfsync everysec
	```
4. To confirm storage is ok 
	```bash 
	dir /var/lib/redis
	```
5. Make redis instance to be master 
	```bash 
	eplicaof no one
	```
6. Se Maxmemory-policy 
```bash 
maxmemory-policy noeviction
```
1. Restart redis 
	```bash 
	sudo systemctl restart redis.service
	```
#### Redis Socat 
1. Create a service 
	```bash 
	# sudo nano /etc/systemd/system/socat-redis.service
	[Unit]
	Description=Socat Port Forward for Redis
	After=network.target
	
	[Service]
	ExecStart=/usr/bin/socat TCP-LISTEN:6379,fork TCP:clustercfg.test-teslm.npeqy6.euc1.cache.amazonaws.com:6379
	Restart=always
	RestartSec=3
	User=nobody
	StandardOutput=syslog
	StandardError=syslog
	
	[Install]
	WantedBy=multi-user.target
	
	
	```
1. Restart and enable server 
	```bash 
	sudo systemctl daemon-reload
	sudo systemctl enable socat-redis
	sudo systemctl start socat-redis
	sudo systemctl status socat-redis 
	```
#### Useful Redis commands 
1. Check memory 
	```bash 
	info memory 
	```

#### Redis Presenstent 
- Redis Database (RDB). 
	- Taking snapshot from the current Data. 
	- The default value, to verify that run the following command 
		```bash 
		CONFIG GET save 
		```
