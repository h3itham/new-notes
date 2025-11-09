#### General 
- Kubernetes system components communicate only with API server.
- They don't talk to each other directly. 
- There can be run more than one instance of each control plan component to ensure high availability.
- The Only component that talk directly with etcd is *API server*.

- <span style='color:red'>what the API server dose?</span> 
  It provide a <span style='color:cyan'>CRUD</span> (Create, Read, Update & Delete) interface for querying and modifying the cluster state over a RestFull API. 
  ![](api-server.png) 
- You can run Kubernetes cluster without having scheduler, but you need to schedule it manually. 
- Controller Manager consist from different controllers, every object in kubernetes has i't own implementation.  
  For example (Node controller, Replication Controller, Endpoint controllers, .....). 
-  <span style='color:red'>What is static pod?</span> 
   Kubernetes Pod which manged directly from kublet rather than to be managed with API server. 
   **POC**
   1. Create Pod Configuration file 
    ```yaml 
    apiVersion: v1
	kind: Pod
	metadata:
	  name: static-nginx
	  namespace: default
	spec:
	  containers:
	  - name: nginx
	    image: nginx:latest
	    ports:
	    - containerPort: 80
	```

2. Place configuration file in the following specific folder 
   ```txt
     /etc/kubernetes/manifests/
	 ```
	 Kubelet will automatically detect it and start the pod you can check 
	 ```bash 
	 kubectl get pods -o wide 
	  ```

- **Kube-Proxy:** Responsible for Networking in kubernetes cluster 
	- The proxy configure IPtables rules. 
	- IPtables is the tool which managing Linux Kernel's packets
	- The following was the old implementation. 
	  ![](kubeproxy.png)
	- Current configuration 
	  ![](kproxy.png)
	  