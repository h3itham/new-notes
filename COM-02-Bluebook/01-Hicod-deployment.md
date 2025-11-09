#### Database
-  Create Network 
	```bash 
	docker network create --subnet=192.168.1.0/24 hicode
	```

-  Create Database Volume 
	```bash 
	docker volume create database-vol 
	```
-  Create Database Instance 
	```bash
	docker run -itd \
	  --name database \
	  --network hicode \
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

- Restore Database 
	```bahs 
	mysql -u root -phaitham -P 3366 -h 86.48.3.99 hicodeProd < prod-2.sql
	```
	
##### Production

 - Volumes 
	```bash 
	docker volume create prod-static 
	docker volume create prod-media
	```
 - Application
	```bash 
	docker run -itd \
	  --name hicode-prod \
	  --net hicode \
	  --restart always \
	  --ip 192.168.1.20 \
	  -e DB_NAME=hicodeProd \
	  -e DB_HOST=192.168.1.2 \
	  -e DB_PORT=3306 \
	  -e DB_USER=root \
	  -e DB_PASSWORD=haitham\
	  -v prod-static:/app/static \
	  -v prod-media:/app/media \
	  -v /home/haitham/hicode/prod.py:/app/project/settings.py \
	  h3itham/hicode:v0.0.2

	```
- Nginx 
	```bash 
	docker run -itd \
	  --name nginx-hicode\
	  --net hicode \
	  --restart always \
	  --ip 192.168.1.21 \
	  -v  prod-static:/app/static \
	  -v  prod-media:/app/media \
	  -v /home/haitham/hicode/prod.conf:/etc/nginx/conf.d/default.conf \
	  nginx
	```

#### Staging 
- Volumes 
	```bash 
	docker volume create staging-static 
	docker volume create staging-media
	```

- Application 
	```bash 
	docker run -itd \
	  --name hicode-stage \
	  --net hicode \
	  --ip 192.168.1.10 \
	  -e DB_NAME=hicodeStaging \
	  -e DB_HOST=192.168.1.2 \
	  -e DB_PORT=3306 \
	  -e DB_USER=root \
	  -e DB_PASSWORD=haitham\
	  -v staging-static:/app/static \
	  -v staging-media:/app/media \
	  -v /home/haitham/hicode/stage.py:/app/project/settings.py \
	  h3itham/hicode:v1.2.8
	```

- staging 
```bash 
	docker run -itd \
	  --name nginx-stage\
	  --net hicode \
	  --ip 192.168.1.11 \
	  -v  staging-static:/app/static \
	  -v  staging-media:/app/media \
	  -v /home/haitham/hicode/stage.conf:/etc/nginx/conf.d/default.conf \
	  nginx
```

#### Nginx - Proxy 

```bash 
docker run -itd \
  --name proxy \
  --net hicode \
  --restart always \
  --ip 192.168.1.250 \
  -p 80:80 \
  -p 443:443 \
  -v /home/haitham/hicode/default.conf:/etc/nginx/conf.d/default.conf \
  nginx

```

#### Restore backup 
1. backup existing Database 
```bash 
mysqldump -u root -phaitham -P 3366 -h 86.48.3.99 > hicode-data.sql 
```
Delete existing Database 
2. restore form bluebook server 
```bash
mysql -u root -phaitham -P 3366 -h  86.48.3.99 hicodeProd < [backupfile]
```


#### Monitroing 
1. Node exporter 
```bash 
docker run -d \
  --name mon-nodeExporter \
  --restart unless-stopped \
  --network hicode \
  --ip 192.168.1.175 \
  -v /proc:/host/proc:ro \
  -v /sys:/host/sys:ro \
  -v /:/rootfs:ro \
  prom/node-exporter:latest \
    --path.procfs=/host/proc \
    --path.sysfs=/host/sys \
    --path.rootfs=/rootfs \
    --collector.filesystem.ignored-mount-points='^/(sys|proc|dev|host|etc)($|/)'
```

2. CAdviosor 
```bash 
	docker run -d \
	  --name mon-cadvisor \
	  --restart unless-stopped \
	  --network hicode \
	  --ip 192.168.1.176 \
	  -v /:/rootfs:ro \
	  -v /var/run:/var/run:rw \
	  -v /sys:/sys:ro \
	  -v /var/lib/docker/:/var/lib/docker:ro \
	  -v /dev/disk/:/dev/disk:ro \
	  --device /dev/kmsg:/dev/kmsg \
	  gcr.io/cadvisor/cadvisor:latest
	
```