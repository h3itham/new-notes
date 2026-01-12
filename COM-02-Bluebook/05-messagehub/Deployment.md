- Docker command 
	```bash 
	docker run -d --name messagehub \
	  -p 3000:3000 \
	  -e DB_DIALECT=mysql \
	  -e DB_HOST=147.79.114.89 \
	  -e DB_PORT=3306 \
	  -e DB_NAME=messagehub \
	  -e DB_USER=root \
	  -e DB_PASS=teslmpass \
	  -v $(pwd)/wwebjs_auth:/usr/src/app/.wwebjs_auth \
	  messagehub:v1
	```