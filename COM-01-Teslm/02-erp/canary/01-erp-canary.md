1. All network exist on one EC2 
	- Machine IP `18.194.186.133`
2. Create Network 
	```bash
	docker network create \
	  --driver bridge \
	  --subnet 192.168.1.0/24 \
	  erp
	```
3. Create Postgres
	- Create Volume 
		```bash 
		docker volume create mysqldb
		```
	- Create Instance 
		```bash 
		docker run -d \
		  --name erp-db \
		  --network erp \
		  --ip 192.168.1.2 \
		  -e MYSQL_ROOT_PASSWORD=teslmpass \
		  -e MYSQL_DATABASE=canaryerp \
		  -e MYSQL_USER=teslmuser \
		  -e MYSQL_PASSWORD=teslmpass \
		  -v mysqldb:/var/lib/mysql \
		  -p 3306:3306 \
		  mariadb:10.6 \
		  --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci
		```
