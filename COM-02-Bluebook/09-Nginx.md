1. Create Volumes 
	```bash 
	docker volume create nginx-etc
	docker volume create nginx-logs
	```
2. Run Container 
	```bash 
	docker run -itd \
	  --name nginx \
	  --restart=always \
	  --network bluebook \
	  --ip 192.168.1.180 \
	  -p 80:80 \
	  -p 443:443 \
	  -v nginx-etc:/etc:rw \
	  -v nginx-logs:/var/log/nginx:rw \
	  -v systimate-dev-static:/systimate-dev/static \
	  -v systimate-dev-media:/systimate-dev/media \
	  -v systimate-prod-static:/systimate-prod/static \
	  -v systimate-prod-media:/systimate-prod/media \
	  -v ali-prod-static:/ali-prod/static \
	  -v ali-prod-media:/ali-prod/media \
	  -v bluebook-prod-static:/bluebook-prod/static \
	  -v bluebook-prod-media:/bluebook-prod/media \
	  -v imad-prod-static:/imad-prod/static \
	  -v imad-prod-media:/imad-prod/media \
	  nginx:1.27 
	```
3. Install tools 
	```bash 
	apt-get update && apt-get install -y curl vim certbot python3-certbot-nginx apache2-utils
	```

#### Set password for dev 

1. Install `htpasswd` utility 
	```bash 
	apt install apache2-utils
	```
2. Create user (You will prompt to enter password towice)
	```bash 
	htpasswd -c /etc/nginx/.htpasswd bluebook
	```
3. Add this part in the website nginx configuration in server block 
	```nginx
	# Password protection for <siteName>
	auth_basic "Development Area - Restricted Access";
	auth_basic_user_file /etc/nginx/.htpasswd;
	```
4. set proper permissions 
	```bash 
	chown root:nginx /etc/nginx/.htpasswd 
	chmod 640 /etc/nginx/.htpasswd
	```
5. Restart nginx 
	```bash 
	nginx -s reload 
	```