 MongoDB Backup 
```bash 
mongodump --uri="mongodb://backend:backend@192.168.3.225:27017/?authMechanism=SCRAM-SHA-256" --db=cta_transactional_db --out=/root/Mongo/Transactional_DB_$(date +%d-%m-%Y)
```
 
 from cluster 
```bash
mongodump --uri="mongodb://backend:backend@192.168.2.65:27017,192.168.2.66:27017,192.168.2.67:27017/cta_transactional_db?replicaSet=rs0&authSource=admin&authMechanism=SCRAM-SHA-256" --out=/tmp/Transactional_DB_$(date +\%d-\%m-\%Y)
```
 Restore Mongodb 
```bash 
mongorestore --host 192.168.2.84 --port 27017 \
	             --username root --password example \
	             --authenticationDatabase admin \
	             --db cta_transactional_db .
```

