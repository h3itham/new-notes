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