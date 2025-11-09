
#### Introduction 
- A high-performance **web server**, **reverse proxy**, and **load balancer**.
- Support **HTTP, HTTPS, TCP, and UDP** traffic.
- Can cache content to improve performance. 
-  Nginx dose not have any ability to process dynamic content natively. To handle PHP and other requests. Nginx must pass to an external processor for execution and wait for the rendered content to be sent back.
- **Http Response codes** 
	1. `1XX` Informational. 
	2. `2XX` Success
	3. `3XX` ReDirection
		- `304` Redirect client to the same page. 
	4. `4XX` Client error 
	5. `5XX` server error
- Nginx Vocabulary 
	- **Directive**: option and option value ended with simi-colon
	- **Context**: Group of one or more directive
	- **load balancer:** hardware/software that distribute the load of incomming requestes
	- **Proxy:** Intermediary Server between Clients and Internet or other servers/services , which intercept requests from client and forward them to Internet.
		![[proxy.gif]]
	- **Reverse Prox**y: Intermediary Server between clients and backend servers, which route request to appropriate backend server.
		![[reverseproxy.gif]]

#### Timeout Configurations 
- Critical for performance, stability and security. 
- Define how long nginx waits for certain operations before giving up or closing connection. 
- Frontend timeout settings (client <--> Nginx)

| Timeout Directive           | Description                                                     | Default Value | Context                |
| --------------------------- | --------------------------------------------------------------- | ------------- | ---------------------- |
| `client_header_timeout`     | Timeout for reading the client request header.                  | 60s           | http, server           |
| `client_body_timeout`       | Timeout for reading the client request body.                    | 60s           | http, server           |
| `keepalive_timeout`         | How long to keep a client connection alive (HTTP Keep-Alive).   | 75s           | http, server           |
| `send_timeout`              | Timeout for sending response to the client.                     | 60s           | http, server, location |
| `lingering_time`            | Time to keep connection open after response to read more data.  | 30s           | http, server           |
| `lingering_timeout`         | Time to wait for client to close connection before force close. | 5s            | http, server           |
| `reset_timedout_connection` | Send RST instead of FIN when client times out.                  | off           | http, server           |
- Backend timeout settings (Nginx <--> Backend)

| Timeout Directive          | Description                                                        | Default Value | Context              |
|---------------------------|--------------------------------------------------------------------|---------------|----------------------|
| `proxy_connect_timeout`   | Timeout for connecting to a proxied server.                        | 60s           | http, server, location |
| `proxy_send_timeout`      | Timeout for sending request to the proxied server.                 | 60s           | http, server, location |
| `proxy_read_timeout`      | Timeout for reading response from the proxied server.              | 60s           | http, server, location |
| `fastcgi_connect_timeout` | Timeout for connecting to FastCGI server.                          | 60s           | http, server, location |
| `fastcgi_send_timeout`    | Timeout for sending data to FastCGI server.                        | 60s           | http, server, location |
| `fastcgi_read_timeout`    | Timeout for reading response from FastCGI server.                  | 60s           | http, server, location |
| `uwsgi_connect_timeout`   | Timeout for connecting to uWSGI server.                            | 60s           | http, server, location |
| `uwsgi_send_timeout`      | Timeout for sending data to uWSGI server.                          | 60s           | http, server, location |
| `uwsgi_read_timeout`      | Timeout for reading data from uWSGI server.                        | 60s           | http, server, location |
#### Nginx Security 
In this section you will know how to protect your server from common attacks (DDoS, SQLi, XSS, brute force, etc.)
1. **Disable Server Tokens (Hide NGINX Version)**
	- Edit your main config file (`/etc/nginx/nginx.conf`) and add inside the `http` block:
		```js
		server_tokens off;
		```
2. **Rate Limiting & DDoS Protection**
	1. Open `/etc/nginx/nginx.conf` and add the following to `http` section 
		```nginx 
		 limit_req_zone $binary_remote_addr zone=zoneName:10m rate=6r/s;
		```
	2. Open `/etc/nginx/conf.d/default.con` and add the following to `server section`
		```nginx 
		 limit_req zone=ZoneName burst=20 nodelay;
		```
#### Nginx Caching 
NGINX can be configured as a powerful caching server to improve performance by storing responses from proxied servers.
1. Define `cahce path` in `nginx.conf` configuration file 
	```nginx
	# in http section 
	proxy_cache_path /var/cache/nginx levels=1:2 keys_zone=my_cache:10m inactive=60m use_temp_path=off;
	```
	- `levels`: Directory structure levels
	- `keys_zone`: Shared memory zone name and size (10MB in example)
	- `inactive`: How long items remain cached without being accessed
	- `max_size`: Maximum size of cache (optional but recommended)
2. Add the following in main server configuration in location section. 
	```bash 
	server {
	        # ... other server configuration
	        
	        location / {
	            proxy_cache my_cache;    
	            proxy_pass http://backend;
	            
	            # Cache valid responses for 1 hour
	            proxy_cache_valid 200 302 10m;
	            proxy_cache_valid 404      1m;
	            
	            # Add headers to see cache status
	            add_header X-Proxy-Cache $upstream_cache_status;
	           
         add_header X-Upstream-Response $upstream_http_cache_control;

         # Ensure we can cache despite cookies
         proxy_ignore_headers Set-Cookie;
         proxy_hide_header Set-Cookie;
	        }
	    }
	```

You can check if cache or working or not from Development tools in the browser in `Networking` section in `Response headers`. 
#### Hussin Nasser Labs 
One Nginx and two nodes containers on the same network. 
1. Create Docker Network 
	```bash 
	docker network create nginx-network
	```
2. Create two dirs app, nginx 
	```js 
	mkdir nginx app 
	cd app
	```
3. Simple node.js app 
	```bash 
	vim server.js
	```
	```js 
	const http = require('http');
	const server = http.createServer((req, res) => {
	  res.end(`Hello from ${process.env.HOSTNAME}\n`);
	});
	server.listen(3000, () => console.log('Server running on port 3000'));
	```

4. App Docker file 
	```bash 
	vim Dockerfile 
	```
	```bash 
	FROM node:18-alpine
	WORKDIR /app
	COPY server.js .
	EXPOSE 3000
	CMD ["node", "server.js"]
	```

5. Build node containers 
	```bash 
	docker build -t nodejs-app .
	docker run -itd --name nodejs-1 --hostname nodejs-1 --network nginx-network -e HOSTNAME=nodejs-1 nodejs-app
	docker run -d --name nodejs-2 --hostname nodejs-2 --network nginx-network -e HOSTNAME=nodejs-2 nodejs-app
	```
6. Nginx Configuration 
	```nginx 
	upstream nodejs_servers {
	    server nodejs-1:3000;
	    server nodejs-2:3000;
	}
	
	server {
	    listen 80;
	
	    location / {
	        proxy_pass http://nodejs_servers;
	    }
	}
	```
7. Run Nginx container 
	```bash 
	docker run -itd --name nginx --hostname nginx --network nginx-network -p 80:80  nginx
	```



