#### Host 
We use prometheus and grafana to monitor all servers. 
1. . Create Docker volume for prometheus and grafana 
	```bash 
	docker volume create prometheus_data
	docker volume create grafana_data
	```
2.  Prometheus container 
	```bash 
		docker run -d \
	  --name mon-prometheus \
	  --network bluebook \
	  --ip 192.168.1.174 \
	  -p 9090:9090 \
	  -v /home/haitham/prometheus:/etc/prometheus \
	  -v prometheus_data:/prometheus \
	  prom/prometheus:latest \
	  --config.file=/etc/prometheus/prometheus.yml \
	  --storage.tsdb.path=/prometheus \
	  --web.console.libraries=/etc/prometheus/console_libraries \
	  --web.console.templates=/etc/prometheus/consoles \
	  --storage.tsdb.retention.time=15d \
	  --web.enable-lifecycle

	```
3.  Grafana Container 
	```bash 
	docker run -d \
	  --name mon-grafana \
	  --network bluebook \
      --ip 192.168.1.173 \
	  -p 3000:3000 \
	  -e GF_SECURITY_ADMIN_PASSWORD=$PASS \
	  -e GF_USERS_ALLOW_SIGN_UP=false \
	  -v grafana_data:/var/lib/grafana \
	  -v $(pwd)/grafana/provisioning:/etc/grafana/provisioning \
	  grafana/grafana:latest
	```
#### Client 
- Node Exporter for host Related Data 
- cAdvisor for container Related Data 
- Node Exporter 
	```bash 
	docker run -d \
	  --name mon-nodeExporter \
	  --restart unless-stopped \
	  --network bluebook \
	  --ip 192.168.1.175 \
	  -v /proc:/host/proc:ro \
	  -v /sys:/host/sys:ro \
	  -v /:/rootfs:ro \
	  prom/node-exporter:latest \
	    --path.procfs=/host/proc \
	    --path.sysfs=/host/sys \
	    --path.rootfs=/rootfs \
	    --collector.filesystem.ignored-mount-points='^/(sys|proc|dev|host|etc)($|/)'
	```

- CAdviosr 
	```bash 
	docker run -d \
	  --name mon-cadvisor \
	  --restart unless-stopped \
	  --network bluebook \
	  --ip 192.168.1.176 \
	  -v /:/rootfs:ro \
	  -v /var/run:/var/run:rw \
	  -v /sys:/sys:ro \
	  -v /var/lib/docker/:/var/lib/docker:ro \
	  -v /dev/disk/:/dev/disk:ro \
	  --device /dev/kmsg:/dev/kmsg \
	  gcr.io/cadvisor/cadvisor:latest
	
	```

- nginx configuration 
	```nginx 
	server {
		listen 80;
		server_name 62.171.171.26;
	
		location /node/ {
			proxy_pass http://192.168.1.175:9100/;  
			allow 62.171.171.26;  
			deny all;
			proxy_redirect off;
		}
	
		location /cadvisor/ {
			proxy_pass http://192.168.1.176:8080/; 
			allow 62.171.171.26; 
			deny all;
			proxy_redirect off;
		}
	}
	```

- Test prometheus command 
	```bash 
	 curl -s http://localhost:9090/api/v1/targets | jq '.data.activeTargets[] | .labels.job + ": " + .health'
	```



#### Loki & promtail 
1. Create volumes for loki 
	```bash 
	# Create volumes for Loki data and configuration
	docker volume create loki_data
	docker volume create loki_config
	```
2. Create config filea nd add it to `prometheus` repo 
3. Create loki container 
	```bash
	docker run -d \
	  --name mon-loki \
	  --network bluebook \
	  --ip 192.168.1.181 \
	  -p 3100:3100 \
	  -v loki_data:/loki \
	  -v /home/haitham/prometheus/loki-config.yaml:/etc/loki/loki-config.yaml \
	  -v /usr/share/zoneinfo/Africa/Cairo:/etc/localtime:ro \
	  -e TZ=Africa/Cairo \
	  grafana/loki:latest \
	  -config.file=/etc/loki/loki-config.yaml
	```
4. Add promatil file in the `prometheus` repo 
5. Create promtail container 
	```bash
	docker run -d \
	  --name mon-promtail \
	  --network bluebook \
	  --ip 192.168.1.182 \
	  -v /var/run/docker.sock:/var/run/docker.sock \
	  -v /var/lib/docker/containers:/var/lib/docker/containers:ro \
	  -v /home/haitham/prometheus/promtail-config.yaml:/etc/promtail/promtail-config.yaml \
	  -v /usr/share/zoneinfo/Africa/Cairo:/etc/localtime:ro \
	  -e TZ=Africa/Cairo \
	  grafana/promtail:latest \
	  -config.file=/etc/promtail/promtail-config.yaml
	```