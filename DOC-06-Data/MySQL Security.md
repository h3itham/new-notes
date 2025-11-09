
#### General Log 
> <span style="color:rgb(255, 0, 0)">Note</span>  Enable it in Dev Environment Only. (Degrade Database Performance). 

- General Log records all queries executed by the MySQl server including connections disconnections and queries. 
- To enable it 
	```sql 
	SET GLOBAL log_output = 'FILE';
	SET GLOBAL general_log = 'ON'
	```
- Show log file location 
	```sql 
	SHOW VARIABLES LIKE 'general_log_file';
	```
- To disable it 
	```sql 
	SET GLOBAL general_log = 'OFF';
	```

#### Slow Query 
- Records query that take longer than specified amount of time to execute. 
- To Enable it 
	```sql 
	SET GLOBAL slow_query_log = 'ON'; 
	SET GLOBAL long_query_time = 2;   
	```
- Show log file location. 
	```sql 
	SHOW VARIABLES LIKE 'slow_query_log_file';
	```
- To disable it 
	```sql 
	SET GLOBAL slow_query_log = 'OFF';
	```

#### Quick setup 
Configure mysql to use `genral.log` and `slow.log` directly to `my.cnf` file 
```txt 
[mysqld]
general_log = 1
general_log_file = /var/lib/mysql/general.log
slow_query_log = 1
slow_query_log_file = /var/lib/mysql/slow.log
long_query_time = 2
log_output = FILE
```

#### Log Rotation 
- Create Simple script which rotate logs and retain only 5 days from logs `logRotat.sh`
	```bash
	#!/bin/bash
	
	# MySQL data directory
	MYSQL_DATA_DIR="/var/lib/mysql"
	
	# Get the current date
	CURRENT_DATE=$(date +%Y-%m-%d)
	
	# Rename the general log
	if [ -f "$MYSQL_DATA_DIR/general.log" ]; then
	    mv "$MYSQL_DATA_DIR/general.log" "$MYSQL_DATA_DIR/general_$CURRENT_DATE.log"
	fi
	
	# Rename the slow query log
	if [ -f "$MYSQL_DATA_DIR/slow.log" ]; then
	    mv "$MYSQL_DATA_DIR/slow.log" "$MYSQL_DATA_DIR/slow_$CURRENT_DATE.log"
	fi
	
	# Flush logs to apply changes
	mysql -e "FLUSH LOGS;"
	
	# Delete log files older than 5 days
	find "$MYSQL_DATA_DIR" -name "general_*.log" -mtime +5 -exec rm -f {} \;
	find "$MYSQL_DATA_DIR" -name "slow_*.log" -mtime +5 -exec rm -f {} \;
	```
	

- add script to crontab job. 
	```bash
	crontab -e
	0 0 * * * /path/to/your/script.sh
	```

