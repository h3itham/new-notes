#### Nginx Ingress Customization. 
1. Allow configuration in annotations 
	```bash
	kubectl edit configmap -n ingress-nginx nginx-ingress-ingress-nginx-controller
	```
2. The problem of Forwarding the Client IP address. 
   The issue lies in the `externalTrafficPolicy` setting of your NGINX Ingress controller Service. Currently, it is set to `Cluster`, which means the original client IP is not preserved, and the worker node IP is used instead. To forward the **actual client IP** to your backend, you need to set `externalTrafficPolicy: Local`.
   Follow the following link
   `https://www.reddit.com/r/kubernetes/comments/16lz4n7/preserving_source_ip_with_ingressnginx/`

