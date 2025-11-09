#### Database
-  Create Network 
	```bash 
	docker network create --subnet=192.168.1.0/24 bluebook
	```

-  Create Database Volume 
	```bash 
	docker volume create database-vol 
	```
-  Create Database Instance 
	```bash
	docker run -itd \
	  --name database \
	  --network bluebook \
	  --restart always \
	  --ip 192.168.1.2 \
	  -p 3366:3306 \
	  -e TZ=Africa/Cairo \
	  -e MYSQL_ROOT_PASSWORD=Blueb00k2024 \
	  -e MYSQL_DATABASE=bluebookProd  \
	  -e MYSQL_USER=bluebook \
	  -e MYSQL_PASSWORD=Blueb00k2024 \
	  -v database-vol:/var/lib/mysql \
	  mysql:8.0
	```
- Allow Remote root login 
	```sql 
	ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'Blueb00k2024';
	GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;
	FLUSH PRIVILEGES;
	```

- Restore Database 
	```bahs 
	mysql -u root -phaitham -P 3366 -h 86.48.3.99 hicodeProd < prod-2.sql
	```
	

#### production (hi-code server)
- Create volume 
```bash 
docker volume create bluebook-prod-static 
docker volume create bluebook-prod-media
```
- Note that you use network on the server which name is `hicode` 
- Application 
	```bash
	docker run -itd \
  --name bluebook-prod \
  --restart always \
  --network bluebook \
  --ip 192.168.1.100 \
  -e DB_NAME=bluebookProd \
  -e DB_HOST=192.168.1.2 \
  -e DB_PORT=3306 \
  -e DB_USER=root \
  -e TZ=Africa/Cairo \
  -e DB_PASSWORD=Blueb00k2024 \
  -v bluebook-prod-static:/app/static \
  -v bluebook-prod-media:/app/media \
  -v /home/haitham/bluebook/prod.py:/app/project/settings.py \
  h3itham/bluebook:v0.0.3
	```


- Nginx 
```bash 
docker run -dit \
  --name nginx-bluebook \
  --restart always \
  --network bluebook \
  --ip 192.168.1.101 \
  -v bluebook-prod-static:/app/static \
  -v bluebook-prod-media:/app/media \
  -v ~/bluebook/prod.conf:/etc/nginx/conf.d/default.conf \
  -p 80:80 \
  -p 443:443 \
  nginx:latest

```

- SSL Configuration 
	```bash 
	apt install certbot python3-certbot-nginx 
	certbot --nginx -d bluebook.iceage.me.uk
	```