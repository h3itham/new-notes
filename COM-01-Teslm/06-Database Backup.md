- Create S3 bucket dedicated for backup `teslm-db-backup`
- install dependencies 
```bash
sudo apt-get update
sudo snap install aws-cli --classic
sudo apt-get install postgresql-client
```
### Backup and Restore 

- Command to backup (Teslm-dev) machine
	```bash 
	PGPASSWORD='teslmpass' pg_dumpall -h 127.0.0.1 -U teslmuser > complete_backup_$(date +%Y%m%d_%H%M%S).sql
	```
- Restore 
	- Install client 
		```bash 
		sudo apt update 
		sudo apt install postgres-client
		```
		```bash 
		psql -h teslm-staging-db.cdqy8mciq6nl.eu-central-1.rds.amazonaws.com \
		     -U teslm_admin \
		     -d teslm \
		     -f postgres_canary_2025-10-31_18-09-33.sql
	    # will prompt password enter it 	
		```
#### Roles 
- Roles: Represent user or group that can own database objects
	- manage authentication and authorization
	- List all roles 
		```bash 
		PGPASSWORD="teslmpass" psql -h 127.0.0.1 -p 5432 -U teslmuser -d postgres -c "\du"
		```
	- Backup hungerstation role 
		```bash
		PGPASSWORD="teslmpass" pg_dumpall -h 127.0.0.1 -p 5432 -U teslmuser --roles-only | grep -A 10 "hunger-station_owner" > /tmp/hungerstation_role.sql
		```
	- Restore hugerstation role 
		```bash
		psql -U teslm_admin -h teslm-staging-db.cdqy8mciq6nl.eu-central-1.rds.amazonaws.com  -d teslm -f hungerstation_role.sql
		```
	- Make ensure that role is ok 
		```bash
		psql -U teslm_admin -h teslm-staging-db.c5o8iuigo0b9.eu-central-1.rds.amazonaws.com -d postgres -c "\du"
		```
	- Grant all `hunger_station_owner` to `teslm_user`
		```bash 
		PGPASSWORD='teslmpassword123!' psql -h 3.72.16.66 -U teslm_admin -d teslm -c "GRANT \"hunger-station_owner\" TO teslm_admin;"
		```

#### Socat part 
1. setup and install socat 
	```bash 
	sudo apt update 
	sudo apt install socat 
	```
2. Fireup socat 
	```bash 
	socat TCP-LISTEN:5432,fork TCP:teslm-staging-db.cdqy8mciq6nl.eu-central-1.rds.amazonaws.com:5432
	```
#### Troubleshouting Migrations 
This approche build on top of using docker container which run from the excalty image which just build from the pipeline 
1. Authenticate with Artiface 
	```bash 
	aws ecr get-login-password --region eu-central-1 \
	  | docker login --username AWS --password-stdin 242945234432.dkr.ecr.eu-central-1.amazonaws.com
	```
2. Get the image from ECR 
3. Run container which show the database migrations 
	```bash 
	docker run --rm \
	  -e POSTGRES_HOST=3.72.241.153 \
	  -e POSTGRES_DATABASE=postgres \
	  -e POSTGRES_USER=teslm_admin \
	  -e POSTGRES_PASSWORD='teslmpassword123!' \
	  -e POSTGRES_PORT=5432 \
	  -e DB_SSL=true \
	  242945234432.dkr.ecr.eu-central-1.amazonaws.com/staging-container-repo:4ef2f8b7f5f498b4fde03f26d3bea7db92f309aa \
	  sh -c "ls -la dist/src/db/migrations || true; npx typeorm -d dist/src/db/data-source.js migration:show"
	```
4. If `migration:show` lists pending migrations and you want to apply them
	```bash 
	docker run --rm \
	  -e POSTGRES_HOST=3.72.241.153 \
	  -e POSTGRES_DATABASE=postgres \
	  -e POSTGRES_USER=teslm_admin \
	  -e POSTGRES_PASSWORD='teslmpassword123!' \
	  -e POSTGRES_PORT=5432 \
	  -e DB_SSL=true \
	  242945234432.dkr.ecr.eu-central-1.amazonaws.com/staging-container-repo:4ef2f8b7f5f498b4fde03f26d3bea7db92f309aa \
	  sh -c "ls -la dist/src/db/migrations || true; npx typeorm -d dist/src/db/data-source.js migration:show && npx typeorm -d dist/src/db/data-source.js migration:run"
	```
5. Check if the migration applied or not 
```bash 
MIGRATION_NAME="TestMigrations1763639972450"
PGPASSWORD='teslmpassword123!' psql -h 3.72.241.153 -U teslm_admin -d postgres -At -c \
  "SELECT 1 FROM canary.migration WHERE name = '$MIGRATION_NAME' LIMIT 1" \
  | grep -q 1 && echo "APPLIED" || echo "NOT APPLIED"
```