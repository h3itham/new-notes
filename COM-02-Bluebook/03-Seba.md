#### Production 
1. I will use Hicode network `hicode`
2. Application 
	```bash 
	docker run -itd \
	  --name seba-prod \
	  --net hicode \
	  --restart always \
	  --ip 192.168.1.60 \
	  h3itham/seba:v0.0.0
	```

3. Add the following Nginx configuration to `proxy` container 
	```nginx
	server {
	    listen 80;
	    server_name sebalenses.com;
	    
	    location / {
	        proxy_pass http://192.168.1.60:80;
	        proxy_set_header Host $host;
	        proxy_set_header X-Real-IP $remote_addr;
	        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
	        proxy_set_header X-Forwarded-Proto $scheme;
	    }
	}
	```
4. SSL configuration 
	```bash 
			apt install certbot python3-certbot-nginx 
		certbot --nginx -d sebalenses.com
	```

