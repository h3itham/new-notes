```yml
# TESLM Simple Nginx Configuration
client_max_body_size 100M;
server {
    server_name canary-api.teslm.shop;

    # Main API and everything else
    location / {
        proxy_pass http://192.168.1.4:4050;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
	proxy_set_header Authorization $http_authorization;

    }

    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/canary-api.teslm.shop/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/canary-api.teslm.shop/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot



}

server {
    server_name canary-chat.teslm.shop;

    location / {
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $host;
        proxy_pass http://192.168.1.4:5051;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_read_timeout 3600s;
	proxy_send_timeout 3600s;
	proxy_connect_timeout 60s;

    }

    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/canary-api.teslm.shop/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/canary-api.teslm.shop/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot



}

server {
    server_name canary-delivery.teslm.shop;

    location / {
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $host;
        proxy_pass http://192.168.1.4:5052;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
	proxy_read_timeout 3600s;
	proxy_send_timeout 3600s;
	proxy_connect_timeout 60s;
    }

    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/canary-api.teslm.shop/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/canary-api.teslm.shop/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot



}

server {
    server_name canary-notifications.teslm.shop;

    location / {
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $host;
        proxy_pass http://192.168.1.4:5068;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_read_timeout 3600s;
	proxy_send_timeout 3600s;
	proxy_connect_timeout 60s;

    }

    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/canary-api.teslm.shop/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/canary-api.teslm.shop/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot



}

server {
    if ($host = canary-api.teslm.shop) {
        return 301 https://$host$request_uri;
    } # managed by Certbot


    listen 80;
    server_name canary-api.teslm.shop;
    return 404; # managed by Certbot


}

server {
    if ($host = canary-chat.teslm.shop) {
        return 301 https://$host$request_uri;
    } # managed by Certbot


    listen 80;
    server_name canary-chat.teslm.shop;
    return 404; # managed by Certbot


}

server {
    if ($host = canary-delivery.teslm.shop) {
        return 301 https://$host$request_uri;
    } # managed by Certbot


    listen 80;
    server_name canary-delivery.teslm.shop;
    return 404; # managed by Certbot


}

server {
    if ($host = canary-notifications.teslm.shop) {
        return 301 https://$host$request_uri;
    } # managed by Certbot


    listen 80;
    server_name canary-notifications.teslm.shop;
    return 404; # managed by Certbot


}
```