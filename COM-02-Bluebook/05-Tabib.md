#### Ali Zaqzouk Deployment (production)
- Deployed on the `bluebook` server. 
- Create volumes 
	```bash 
	docker volume create ali-prod-static 
	docker volume create ali-prod-media
	```
- Note that you use network on the server which name is `bluebook` 
- Application Deployed with github action 
- Internal Nginx 
	```bash 
		docker run -itd \
		  --name nginx-ali-tabib-prod\
		  --net bluebook \
		  --restart always \
		  --ip 192.168.1.171 \
		  -v  ali-prod-static:/app/static \
		  -v  ali-prod-media:/app/media \
		  -v /home/haitham/tabib/interanl-nginx.conf:/etc/nginx/conf.d/default.conf \
		  nginx
	```

- SSL Configuration 
	```bash 
	apt install certbot python3-certbot-nginx 
	# Update and install required packages

	certbot --nginx -d alizakzouk.online
	```