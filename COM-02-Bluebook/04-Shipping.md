

#### Staging 
- Volumes 
	```bash 
	docker volume create shipping-stage-static 
	docker volume create shipping-stage-media
	```
- Application
	```bash 
	docker run -itd \
	  --name shipping-stag \
	  --net stag \
	  --restart always \
	  --ip 192.168.1.30 \
	  -e DB_NAME=shippingStag \
	  -e DB_HOST=192.168.1.2 \
	  -e DB_PORT=3306 \
	  -e DB_USER=root \
	  -e DB_PASSWORD=haitham\
	  -v shipping-stage-static:/app/static \
	  -v shipping-stage-media:/app/media \
	  -v /home/haitham/Shipping-Company/stag.py:/app/project/settings.py \
	  h3itham/shipping:v0.0.2
	```

- Nginx 
	```bash 
		docker run -itd \
		  --name nginx-shipping-stag\
		  --net stag \
		  --restart always \
		  --ip 192.168.1.31 \
		  -v  shipping-stage-static:/app/static \
		  -v  shipping-stage-media:/app/media \
		  -v /home/haitham/Shipping-Company/stag.conf:/etc/nginx/conf.d/default.conf \
		  nginx
	```

#### Prod 
##### Database 
 - Create Network 
	```bash 
	docker network create --subnet=192.168.1.0/24 prod
	```

-  Create Database Volume 
	```bash 
	docker volume create database-vol 
	```
-  Create Database Instance 
	```bash
	docker run -itd \
	  --name database \
	  --network prod \
	  --restart always \
	  --ip 192.168.1.2 \
	  -p 3366:3306 \
	  -e MYSQL_ROOT_PASSWORD=Shipping2024\
	  -e MYSQL_DATABASE=shippingProd\
	  -e MYSQL_USER=shipping \
	  -e MYSQL_PASSWORD=Shipping2024 \
	  -v database-vol:/var/lib/mysql \
	  mysql:8.0
	```
- Allow Remote root login 
	```sql 
	ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'Shipping2024';
	GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;
	FLUSH PRIVILEGES;
	```
#### Application 
- Application
	```bash 
	docker run -itd \
	  --name shipping-prod \
	  --net prod \
	  --restart always \
	  --ip 192.168.1.32 \
	  -e DB_NAME=shippingProd \
	  -e DB_HOST=192.168.1.2 \
	  -e DB_PORT=3306 \
	  -e DB_USER=root \
	  -e DB_PASSWORD=Shipping2024\
	  -v shipping-prod-static:/app/static \
	  -v shipping-prod-media:/app/media \
	  h3itham/shipping:v0.0.2
	```

- Nginx 
	```bash 
		docker run -itd \
		  --name nginx-shipping-prod\
		  --net prod \
		  --restart always \
		  --ip 192.168.1.33 \
		  -v  shipping-prod-static:/app/static \
		  -v  shipping-prod-media:/app/media \
		  -v /home/haitham/Shipping-Company/prod.conf:/etc/nginx/conf.d/default.conf \
		  nginx
	```

#### Nginx - Proxy 

```bash 
docker run -itd \
  --name proxy \
  --net prod \
  --restart always \
  --ip 192.168.1.250 \
  -p 80:80 \
  -p 443:443 \
  -v /home/haitham/Shipping-Company/default.conf:/etc/nginx/conf.d/default.conf \
  nginx

```
#### SLL Configuration 

```bash
apt install certbot python3-certbot-nginx 
certbot --nginx -d bluebook.iceage.me.uk
```