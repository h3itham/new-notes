#### Introduction 
I'll use my ready prometheus and grafana in bluebook for monitoring dev backend. 
#### Node exporter 
```bash 
	docker run -d \
	  --name mon-nodeExporter \
	  --restart unless-stopped \
	  --network teslm \
	  --ip 192.168.1.9 \
	  -v /proc:/host/proc:ro \
	  -v /sys:/host/sys:ro \
	  -v /:/rootfs:ro \
	  prom/node-exporter:latest \
	    --path.procfs=/host/proc \
	    --path.sysfs=/host/sys \
	    --path.rootfs=/rootfs \
	    --collector.filesystem.ignored-mount-points='^/(sys|proc|dev|host|etc)($|/)'
```

#### CAdvisor 
```bash 
	docker run -d \
	  --name mon-cadvisor \
	  --restart unless-stopped \
	  --network teslm \
	  --ip 192.168.1.10 \
	  -v /:/rootfs:ro \
	  -v /var/run:/var/run:rw \
	  -v /sys:/sys:ro \
	  -v /var/lib/docker/:/var/lib/docker:ro \
	  -v /dev/disk/:/dev/disk:ro \
	  --device /dev/kmsg:/dev/kmsg \
	  gcr.io/cadvisor/cadvisor:latest
	
```