- Environment 
	- Database 
		```bash 
				docker run -d \
				  --name mariadb-erp \
				  -e MYSQL_ROOT_PASSWORD=teslmpass \
				  -e MYSQL_DATABASE=erp \
				  -e MYSQL_USER=teslmuser \
				  -e MYSQL_PASSWORD=teslmpass \
				  -v mysqlb:/var/lib/mysql \
				  -p 3306:3306 \
				  mariadb:10.6 \
				  --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci
		```
	- Database Connection 
		```bash 
		mysql -h 3.72.108.8  -P 3306 -u teslmuser -p
		```

- Mostafa send to files 
	-  ecs_teslm_main   ---> Backend 
	- teslm_ecommenrce_main --> Frontend 
- We will dockerize this app to use for ECS and EKS. 

environemnt 
	# Database Configuration
	DB_HOST=147.79.114.89
	DB_USER=teslmuser
	DB_PASSWORD=teslmpass
	DB_PORT=3306
	DB_NAME=erp
	
	# Redis Configuration
	REDIS_HOST=147.79.114.89
	REDIS_PORT=6379
	REDIS_PASSWORD=Teslm@2024
 