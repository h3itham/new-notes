#### Steps 
1. Start from `frappe_docker` repo which will be used as a refrence and add the required files in your cutsome application 
	```bash 
	git clone https://github.com/frappe/frappe_docker  
	cd frappe_docker
	```
2. Copy booth `containerfile` and `Nginx` resources file 
	```bash 
	cp images/custom/Containerfile ~/haitham-erp/Dockerfile
	cp -r resources/. ~/haitham-erp/resources
	```
3. Create `apps.json` file inside `resources` directory such the following format 
	```json 
	[  
	  {  
	     "url": "https://{username}:{PAT}@github.com/{project}/{repo}.git",  
	     "branch": "{branch_name}"  
	  }  
	]
	```
	example for `star_ebook_store` repo url `https://github.com/NagariaHussain/star_ebook_store?utm_source=chatgpt.com`
	```json 
	[
	  {
	    "url": "https://{mostafa1021999}:{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}@github.com/NagariaHussain/star_ebook_store.git",
	    "branch": "main"
	  }
	]
	
	```
4. Change Dockerfile to use `app.json` as start point 
	- Add the below command above RUN bench init command 
		```bash 
		COPY resources/apps.json /opt/frappe/apps.json
		```
	- Change bench init command 
		- From 
			```bash 
			RUN export APP_INSTALL_ARGS="" && \  
			if [ -n "${APPS_JSON_BASE64}" ]; then \  
			export APP_INSTALL_ARGS="--apps_path=/opt/frappe/apps.json"; \  
			fi && \  
			bench init ${APP_INSTALL_ARGS}\  
			--frappe-branch=${FRAPPE_BRANCH} \  
			--frappe-path=${FRAPPE_PATH} \  
			--no-procfile \  
			--no-backups \  
			--skip-redis-config-generation \  
			--verbose \  
			/home/frappe/frappe-bench && \  
			cd /home/frappe/frappe-bench && \  
			echo "{}" > sites/common_site_config.json && \  
			find apps -mindepth 1 -path "*/.git" | xargs rm -fr
			```
		- To 
			```bash 
			RUN bench init \
			    --apps_path=/opt/frappe/apps.json \
			    --frappe-path=${FRAPPE_PATH} \
			    --frappe-branch=${FRAPPE_BRANCH} \
			    --no-procfile \
			    --no-backups \
			    --skip-redis-config-generation \
			    --verbose \
			    /home/frappe/frappe-bench && \
			  cd /home/frappe/frappe-bench && \
			  echo "{}" > sites/common_site_config.json && \
			  find apps -mindepth 1 -path "*/.git" | xargs rm -fr
			```

5. Pip Package installation for custom application 
   - Create `requirements.txt` in `resources` Directory. 
   - Change `Dockerfile` to use it 
		```bash 
		COPY resources/requirements.txt /home/frappe/requirements.txt
ENV PATH="/home/frappe/frappe-bench/env/bin:$PATH"
RUN pip install -r /home/frappe/requirements.txt
		```
#### Deploy with docker compose 
1. I make needed changes and save it in repository 
2. Docker compose file start point 
	```bash 
	cp frappe_docker/pwd.yml ~/haitham-erp/compose.yml
	```
3. Change image tag with image which we just built in booth `frontend` & `backend`
4. SSL or secure website. 
```bash 
cp frappe_docker/overrides/compose.https.yaml ~/haitham-erp/traefik.yml
```
#### Resources 

- Mediume [article](https://medium.com/@yashwanthtss7/dockerize-custom-application-in-frappe-framework-migration-from-vm-to-container-bac073ec1040) 
- Article [article](https://medium.com/@omartarekabdelall/deploying-frappe-erpnext-and-hrms-on-kubernetes-using-docker-helm-and-argocd-312578ebe4a9)
