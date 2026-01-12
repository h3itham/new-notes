
####  Commands 
1. Install traefik 
	```bash 
	echo 'TRAEFIK_DOMAIN=erp-canary.teslm.shop' > ~/gitops/traefik.env
	echo 'EMAIL=haithamelabd@outlook.com' >> ~/gitops/traefik.env
	echo "HASHED_PASSWORD='$(openssl passwd -apr1 Teslm2025)'" >> ~/gitops/traefik.env
	```
	```bash 
	docker compose --project-name traefik \
	  --env-file ~/gitops/traefik.env \
	  -f overrides/compose.traefik.yaml \
	  -f overrides/compose.traefik-ssl.yaml up -d
	```
2. Install  Mariadb 
	```bash 
	echo "DB_PASSWORD=Teslm2025" > ~/gitops/mariadb.env
	```
	```bash
	docker compose --project-name mariadb --env-file ~/gitops/mariadb.env -f overrides/compose.mariadb-shared.yaml up -d
	```
3. Install ERP Next 
	- Environment 
		```bash 
		cp example.env ~/gitops/erpnext-one.env
		sed -i 's/DB_PASSWORD=123/DB_PASSWORD=teslmpass/g' ~/gitops/erpnext-one.env
		sed -i 's/DB_HOST=/DB_HOST=3.72.108.8/g' ~/gitops/erpnext-one.env
		sed -i 's/DB_PORT=/DB_PORT=3306/g' ~/gitops/erpnext-one.env
		sed -i 's/SITES=`erp.example.com`/SITES=\`erp-canary.teslm.shop\`,\`two.example.com\`/g' ~/gitops/erpnext-one.env
		echo 'ROUTER=erpnext-one' >> ~/gitops/erpnext-one.env
		echo "BENCH_NETWORK=erpnext-one" >> ~/gitops/erpnext-one.env
		```
	- Create docker compose file 
		```bash 
		docker compose --project-name erpnext-one \
		  --env-file ~/gitops/erpnext-one.env \
		  -f compose.yaml \
		  -f overrides/compose.redis.yaml \
		  -f overrides/compose.multi-bench.yaml \
		  -f overrides/compose.multi-bench-ssl.yaml config > ~/gitops/erpnext-one.yaml
		```

    - Docker compose up 
		```bash 
		docker compose --project-name erpnext-one -f ~/gitops/erpnext-one.yaml up -d
		```

	- Create Site
		```bash
		docker compose --project-name erpnext-one exec backend \
  bench new-site \
  --mariadb-user-host-login-scope=% \
  --db-root-password teslmpass \
  --db-password teslmpass \
  --install-app erpnext \
  --admin-password teslmpass \
  erp-canary.teslm.shop
		```


#### Add Apps 
1. Enter the backend container 
	```bash 
	docker compose --project-name erpnext-one exec backend bash
	```

2. Get the apps 
	```bash 
	bench get-app hrms
	bench get-app lending 
	bench get-app payments
	bench get-app https://github.com/your-repo/ksa
	```
#### Drop Website 

```bash 
docker compose --project-name erpnext-one exec backend \
  bench drop-site erp-canary.teslm.shop --force
```
