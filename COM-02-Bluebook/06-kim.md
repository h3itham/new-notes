#### General Notes 
- Use Image Ubuntu 22 minimal 
#### Database instance 
##### MariaDB 
1. Install Mysql Server on ubuntu 22 
	```bash 
	sudo apt install mysql-server 
	```
2. connect to database 
	```bash 
	sudo mysql -u root -p 
	```
3. Create Password for root 
	1.  Set root password (replace 'your_new_password' with a strong password) 
		```bash 
		ALTER USER 'root'@'localhost' IDENTIFIED BY 'your_new_password';
		```
	
	2. Flush Privileges to apply changes 
		```bash 
		FLUSH PRIVILEGES;
		```
			
	3. exit MariaDB 
		```bash 
		EXIT; 
		```
4. Allow Root Remote Login 
	1. Allo root 
		```mysql 
		GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'your_new_password' WITH GRANT OPTION;
		```
	2. Flush Privilege 
		```mysql 
		FLUSH PRIVILEGES; 
		```
	3. Exit 

5. Allow any user to connect from outside mahcine 
	- Open Mysql Configuration file, and replace `127.0.01` with `0.0.0.0` 
		```bash 
		sudo vim /etc/mysql/mariadb.conf.d/50-server.cnf 
		```
	- Restart Mysql 
		```bash 
		sudo systemctl restart msyql
		```


#### WebServer 
- Run Nginx Container whcih have two Environment volumes 
	```bash 
	docker run -itd \
	  --name nginx-kim \
	  --net kim \
	  --restart always \
	  --ip 192.168.1.200 \
	  -p 80:80 \
	  -p 443:443 \
	  -v kim-static-prod:/app-prod/static \
	  -v kim-media-prod:/app-prod/media \
	  -v kim-static-dev:/app-dev/static \
	  -v kim-media-dev:/app-dev/media \
	  -v /home/haitham/kim/kim-nginx.conf:/etc/nginx/conf.d/default.conf \
	  nginx:alpine
	```
- CertManager 
```bash 
    apk update
    apk add certbot certbot-nginx
	certbot --nginx -d dev.kim-db.com
```