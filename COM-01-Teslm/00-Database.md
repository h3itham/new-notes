#### DevDatabase 
1. Create Network 
	```bash
	docker network create \
	  --driver bridge \
	  --subnet 192.168.1.0/24 \
	  teslm
	
	```
1. Create Postgres
	- Create Volume 
		```bash 
		docker volume create postgresdb
		```
	- Create Instance 
		```bash 
		docker run -d \
		  --name postgres-teslm \
		  --network teslm \
		  --ip 192.168.1.2 \
		  -e POSTGRES_USER=teslmuser \
		  -e POSTGRES_PASSWORD=teslmpass \
		  -e POSTGRES_DB=teslm \
		  -v postgresdb:/var/lib/postgresql/data \
		  -p 5432:5432 \
		  postgres:15
		```
	- Install extensions
		```bash 
		 apt-get install postgresql-contrib postgis postgresql-15-postgis-3
		```
2. Create Mongo 
	- Create volume 
		```bash 
		docker volume create mongodb
		```
	- Create instance 
		```bash
		docker run -d \
		  --name mongo-teslm \
		  --network teslm \
		  --ip 192.168.1.3 \
		  -e MONGO_INITDB_ROOT_USERNAME=admin \
		  -e MONGO_INITDB_ROOT_PASSWORD=secret \
		  -e MONGO_INITDB_DATABASE=teslm-db \
		  -v mongodb:/data/db \
		  -p 27017:27017 \
		  mongo:6.0
		```