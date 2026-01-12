1. exec backend
```bash
aws ecs execute-command \
  --cluster erp-staging \
  --task     16c14bea57fd46edae9a169453b676cc \
  --container backend \
  --region eu-central-1 \
  --interactive \
  --command "/bin/bash"

```
2. exec frontend 
```bash 
aws ecs execute-command \
  --cluster erp-staging \
  --task 070c1e88dc6e4067a98fad8e33efa6d4 \
  --container frontend \
  --region eu-central-1 \
  --interactive \
  --command "/bin/bash"
```
#### Cofigurator 
- Configure website 
	```bash 
	ls -1 /home/frappe/frappe-bench/apps > /home/frappe/frappe-bench/sites/apps.txt
	bench set-config -g db_host erp-staging-db.cdqy8mciq6nl.eu-central-1.rds.amazonaws.com
	bench set-config -gp db_port $DB_PORT
	bench set-config -g redis_cache "redis://$REDIS_CACHE"
	bench set-config -g redis_queue "redis://$REDIS_QUEUE"
	bench set-config -g redis_socketio "redis://$REDIS_QUEUE"
	bench set-config -gp socketio_port $SOCKETIO_PORT
	```

#### Create site 
1. initialize site 
	```bash
	bench new-site \
	  --mariadb-user-host-login-scope='%' \
	  --admin-password admin \
	  --db-root-username root \
	  --db-root-password teslmpass \
	  --db-name=canaryerpp  \
	  --install-app erpnext \
	  --set-default \
	  erp-canary.teslm.shop
	```
	```bash
bench new-site erp-canary.teslm.shop \
  --admin-password admin \
  --db-root-username teslm_admin \
  --db-root-password 'teslmpassword123!' \
  --db-name canaryerpp \
  --mariadb-user-host-login-scope='%' \
  --install-app erpnext \
  --set-default

	```
2. Install apps 
	- Payment
		```bash
		bench --site erp-canary.teslm.shop install-app payments
		```
	- Lending 
		```bash 
		bench --site erp-canary.teslm.shop install-app lending
		```
	- Hrms 
		```bash
		bench --site erp-canary.teslm.shop install-app hrms
		```
	- teslm_ecs 
		```bash
		bench get-app https://mostafa1021999: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@github.com/erpcloudsystems/ecs_teslam.git
		```
	- teslm_ecommerce 
		```bash 
		bench get-app https://mostafa1021999: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@github.com/erpcloudsystems/teslam_ecommerce.git 
		```
2. migrate database 
	```bash
	bench --site erp-canary.teslm.shop migrate --skip-failing;
	```