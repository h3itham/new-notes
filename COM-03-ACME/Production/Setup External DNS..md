1. Just Edit the coredns config with the following 

   ```yaml
	        forward . 192.168.3.203
	```
2. Add hosts Directly to configmab 
```txt 
hosts {
	10.10.0.61  cta-backend.local  
	10.10.0.61  cta-socket.local  
	10.10.0.61  cta-grafana.local  
	10.10.0.61  cta-kibana.local  
	10.10.0.61  cta-ceph.local
}
```

3. Delete CoreDNS deployment 
   