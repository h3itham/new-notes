#### Database 
- Create network 
	```bash 
	docker network create --subnet=192.168.1.0/24 stag 
	```
- Create Database volume
	```bash 
	docker volume create database-vol 
	```
- Create Database Instance 
	```bash 
	docker run -itd \
	  --name database \
	  --network stag \
	  --restart always \
	  --ip 192.168.1.2 \
	  -p 3366:3306 \
	  -e MYSQL_ROOT_PASSWORD=haitham \
	  -e MYSQL_DATABASE=hicode \
	  -e MYSQL_USER=hicode \
	  -e MYSQL_PASSWORD=hicode \
	  -v database-vol:/var/lib/mysql \
	  mysql:8.0
	```
- Allow Remote root login 
	```sql
	ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'haitham';
	GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;
	FLUSH PRIVILEGES;
	```


#### Staging 
- Volumes 
	```bash 
	docker volume create imad-stage-static 
	docker volume create imad-stage-media
	```
- Application
	```bash 
	docker run -itd \
	  --name imad-stag \
	  --net stag \
	  --restart always \
	  --ip 192.168.1.20 \
	  -e DB_NAME=imadStag \
	  -e DB_HOST=192.168.1.2 \
	  -e DB_PORT=3306 \
	  -e DB_USER=root \
	  -e DB_PASSWORD=haitham\
	  -v imad-stage-static:/app/static \
	  -v imad-stage-media:/app/media \
	  -v /home/haitham/imad/stag.py:/app/project/settings.py \
	  h3itham/imad:v2.0.0
	```

- Nginx 
	```bash 
		docker run -itd \
		  --name nginx-imad-stag\
		  --net stag \
		  --restart always \
		  --ip 192.168.1.21 \
		  -v  imad-stage-static:/app/static \
		  -v  imad-stage-media:/app/media \
		  -v /home/haitham/imad/stag.conf:/etc/nginx/conf.d/default.conf \
		  nginx
	```

#### Production 
1. Volumes 
	```bash
	docker volume create imad-prod-static 
	docker volume create imad-prod-media
	```
2. Application 
	```bash 
	docker run -itd \
	  --name imad-prod \
	  --net bluebook \
	  --restart always \
	  --ip 192.168.1.25 \
	  -e DB_NAME=imadProd \
	  -e DB_HOST=192.168.1.2\
	  -e DB_PORT=3306 \
	  -e DB_USER=root \
	  -e DB_PASSWORD=Blueb00k2024 \
	  -v imad-prod-static:/app/static \
	  -v imad-prod-media:/app/media \
	  -v /home/haitham/imad/prod.py:/app/project/settings.py \
	  h3itham/imad:v0.2.0

	```
3. Nginx 
	```bash 
	docker run -itd \
	  --name nginx-imad-prod\
	  --net bluebook \
	  --restart always \
	  --ip 192.168.1.26 \
	  -v  imad-prod-static:/app/static \
	  -v  imad-prod-media:/app/media \
	  -v /home/haitham/imad/prod.conf:/etc/nginx/conf.d/default.conf \
	  nginx
	```
#### Nginx Proxy 
- proxy
	```bash
	docker run -itd \
	  --name proxy \
	  --net stag \
	  --ip 192.168.1.250 \
	  -p 80:80 \
	  -p 443:443 \
	  -v /home/haitham/imad/default.conf:/etc/nginx/conf.d/default.conf \
	  nginx
	```

#### Nginx Configuration 
1. For Reverse proxy 
	```nginx 
	#/etc/nginx/conf.d/imad.conf
	server {
	    server_name nabbiuwny.com;
	    location / {
	        proxy_pass http://192.168.1.26;
	        proxy_set_header Host $host;
	        proxy_set_header X-Real-IP $remote_addr;
	        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
	    }
	
	      location /ws/ {
	        proxy_pass http://192.168.1.26;
	        proxy_http_version 1.1;
	        proxy_set_header Upgrade $http_upgrade;
	        proxy_set_header Connection "upgrade";
	        proxy_set_header Host $host;
	        proxy_set_header X-Real-IP $remote_addr;
	        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
	        proxy_set_header X-Forwarded-Host $server_name;
	        proxy_set_header X-Forwarded-Proto $scheme;
	        proxy_redirect off;
	
	        # WebSocket timeouts
	        proxy_read_timeout 300s;
	        proxy_connect_timeout 300s;
	        proxy_send_timeout 300s;
	    }
	
	
	}
	```

2. main imad nginx 
	```nginx
	server {
	    listen 80;
	    server_name localhost;
	
	    location /static/ {
	        alias /app/static/;
	    }
	
	    location /media/ {
	        alias /app/media/;
	    }
	
	    location / {
	        proxy_pass http://192.168.1.25:8000;
	        proxy_set_header Host $host;
	        proxy_set_header X-Real-IP $remote_addr;
	        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
	        proxy_set_header X-Forwarded-Proto $scheme;
	    }
	
	      location /ws/ {
	        proxy_pass http://192.168.1.25:8000;
	        proxy_http_version 1.1;
	        proxy_set_header Upgrade $http_upgrade;
	        proxy_set_header Connection "upgrade";
	        proxy_set_header Host $host;
	        proxy_set_header X-Real-IP $remote_addr;
	        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
	        proxy_set_header X-Forwarded-Host $server_name;
	        proxy_set_header X-Forwarded-Proto $scheme;
	        proxy_redirect off;
	
	        # WebSocket timeouts
	        proxy_read_timeout 300s;
	        proxy_connect_timeout 300s;
	        proxy_send_timeout 300s;
	    }
	}
	```