- [ ] Make Ensure that DNS can resolve `backend.cta.local` `socket.cta.local` (External DNS) (<span style="color:rgb(255, 0, 0)">Concern</span>)
- [ ] Latest Version of MAPs.
- [ ] Allow Ingress-nginx to support Sockets.
	```bash 
	kubectl edit configmap -n ingress-nginx nginx-ingress-ingress-nginx-controller
	```
	And Allow `configuration-snippet`
- [ ] Backup Job
