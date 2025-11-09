1. Create Volumes 
	```bash 
	docker volume create nginx-etc
	docker volume create nginx-logs
	```
2. Run Container 
	```bash 
	docker run -itd \
	  --name nginx \
	  --restart=always \
	  --network teslm \
	  --ip 192.168.1.5 \
	  -p 80:80 \
	  -p 443:443 \
	  -v nginx-etc:/etc:rw \
	  -v nginx-logs:/var/log/nginx:rw \
	  nginx:1.27 
	```
3. Install tools 
	```bash 
	apt-get update && apt-get install -y curl vim certbot python3-certbot-nginx apache2-utils
	```

4. Certificate 
	```bash 
    certbot --nginx -d canary-api.teslm.shop -d canary-chat.teslm.shop -d canary-delivery.teslm.shop -d canary-notifications.teslm.shop -d kibana.teslm.shop
	```
