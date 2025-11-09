#### DEV
- Create volume 
	```bash 
	docker volume create systimate-dev-static 
	docker volume create systimate-dev-media
	```
- Note that you use network on the server which name is `bluebook` 
- Application (Github Action workflow)
	```bash 
	docker run -itd \
	  --name dev-systimate \
	  --restart always \
	  --network bluebook \
	  --ip 192.168.1.177 \
	  -e DB_NAME=systimateDev \
	  -e DB_HOST=192.168.1.2 \
	  -e DB_PORT=3306 \
	  -e DB_USER=root \
	  -e TZ=Africa/Cairo \
	  -e DB_PASSWORD=Blueb00k2024 \
	  -v systimate-dev-static:/app/static \
	  -v systimate-dev-media:/app/media \
	  -v /home/haitham/bluebook/prod.py:/app/project/settings.py \
	  h3itham/bluebook:v0.0.3
	```
- Nginx pod 
	```bash 
	docker run -itd \
  --name nginx-systimate-dev \
  --net bluebook \
  --restart always \
  --ip 192.168.1.178 \
  -v systimate-dev-static:/app/static \
  -v systimate-dev-media:/app/media \
  -v /home/haitham/systimate/systimate-dev-nginx.conf:/etc/nginx/conf.d/default.conf \
  nginx:alpine
	```
#### PROD 
1. Create Volumes 
	```bash 
		docker volume create systimate-prod-static 
		docker volume create systimate-prod-media
	```
- Note that you use network on the server which name is `bluebook` 
2. Application (Github Action workflow)
	```bash 
	docker run -itd \
	  --name prod-systimate \
	  --restart always \
	  --network bluebook \
	  --ip 192.168.1.178 \
	  -e DB_NAME=systimateDev \
	  -e DB_HOST=192.168.1.2 \
	  -e DB_PORT=3306 \
	  -e DB_USER=root \
	  -e TZ=Africa/Cairo \
	  -e DB_PASSWORD=Blueb00k2024 \
	  -v systimate-dev-static:/app/static \
	  -v systimate-dev-media:/app/media \
	  -v /home/haitham/bluebook/prod.py:/app/project/settings.py \
	  h3itham/bluebook:v0.0.3
	```
- Nginx pod 
	```bash 
	docker run -itd \
  --name nginx-systimate-dev \
  --net bluebook \
  --restart always \
  --ip 192.168.1.178 \
  -v systimate-dev-static:/app/static \
  -v systimate-dev-media:/app/media \
  -v /home/haitham/systimate/systimate-dev-nginx.conf:/etc/nginx/conf.d/default.conf \
  nginx:alpine
	```


#### Restore from backup 
1. 
