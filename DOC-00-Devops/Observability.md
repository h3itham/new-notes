## Introduction 

**Observability:** *understanding* the <span style="color:rgb(255, 255, 0)">internal system state</span> based on data it generates. 

**Observability** Find answers for the following questions 
- What is the state of your system? (<span style="color:rgb(0, 0, 255)">Metrics</span>)
- Why did this happen?                    (<span style="color:rgb(0, 0, 255)">Logging</span>)
- How did this happen?                     (<span style="color:rgb(0, 0, 255)">Traces</span>)

We can say that the three pillars for Observability are: 
1. Metrics 
2. Logging 
3. Traces 

<span style="color:rgb(255, 0, 0)">What is the difference between logs and metrics?</span> 
*Logs:* Time order list of events. 
*Metrics:* is ==Data point== for some measurements. (Historical Data)

What is the difference between Monitoring and Observability? 
- Observability consist from three pillars
	- Metrics 
	- Logging 
	- Traces
- Monitoring consist from one pillars which is Metrics beside to alerts and Dashboards. 
### Prometheus 
- **Prometheus** is an open-source monitoring and alerting tool designed for collecting, storing, and querying time-series metrics
- **Monitoring** platform which collect a wide variety of metrics from various applications. and operating systems. 
- **Time series database** Data is indexed or timestamped in time order. 
- **Prometheus Architecture**
  
  ![](prometheus-architecture.png)
  ![](prometheus-architecture1.png)
  <span style='color:red'>Note</span> Target servers is a server which we need to monitoring, discovered via service discovery or static configuration.   
 **Data Types**
 - String
 - Scalar 
 - Isntant vector: Exactly one sample point per series, taken at a single point in time. 
 - Range vector: multiple data points per series over a time interval 
   ![](instant-vector.png)
	   - Each `x1, x2, ...., y1, y2, ...` together with its corresponding timestamp is called a `sample`
	   - samples within each blue box `(v1, .., v5)` represent an instant vector. 
	   - Samples within each red box `(R2, R4, $5)` represent a range vector 
	     1. - (e.g. foo[3m], assuming samples are scraped per minute):
        
	        - `R3 = [v1, v2, v3]`
            
	        - `R4 = [v2, v3, v4]`
            
	        - `R5 = [v3, v4, v5]`
    range vector is more like a matrix,

- Metric Contain 4 pieces of information 
	1. Metric Name. 
	2. Dimensions 
	3. Value
	4. Timestamp 
		![[metrics.png]]

* When designing a system or applications, its important for teams to set specific measurable targets/goals to help organizations strike the right balance between product development and operations work. From here there is very important concepts you should know 
	* <span style="color:rgb(0, 176, 240)">Service Level Indicator</span> (SLI)
		* Request Latency. 
		* Error Rate. 
		* Saturation. 
		* Availability. 
	* <span style="color:rgb(0, 176, 240)">Service Level Object</span> (SLO) target value or range for an SLI 
		* SLI - Latency
		  SLO - latency < 100ms 
		-  SLI - Availability 
		  SLO - 99.9% uptime 
		SLO should be directly related to the customer experience 
	- <span style="color:rgb(0, 176, 240)">Service Level Agreement</span> (SLA)
		- Contract between a vendor and user that guarantees a certain SLO. 
		
- <span style="color:rgb(255, 0, 0)">What kind of metrics dose prometheus monitors?</span> 
	- CPU / Memory Utilization 
	- Disk space 
	- service uptime 
	- application specific data
##### Metrics Typs 
- **Counter** → A cumulative metric that only increases (e.g., number of requests). It resets only when restarted.
- **Gauge** → A metric that goes up and down (e.g., CPU usage, memory). It represents a value at a specific time.
- **Histogram** → Measures observations (e.g., request durations) and counts them in configurable buckets to analyze distribution.
#### PromQL 
- Short for prometheus Query Language. 
- Data return can be visualized in dashboards.  
- Types  
	1. Simple String 
	2. Scalar or numeric Floating point value. 
	3. Instant Vector 
	4. Range Vector 
  ![](instatant-vector.png)	
  ![](range-vector.png)
## Alerting 
You can define Alert in different channels [ Email, Slack] but first you should decide which resources are important for your application to monitoring and create an alert on them.
![](alerting-configure-notifications-v2.png)

<span style='color:red'>How it works?</span> 
- Granafa Altering periodically queries data and evaluate the condition defined in the alert rule. 
- If the condition is breached, an alert instant fires. 
- Send Notification to defined Chanel  [Email, slak, ...]

You can Inquire the official documentation 
[https://grafana.com/tutorials/alerting-get-started/](https://grafana.com/tutorials/alerting-get-started/) 

### PVC Alert 
1. Create PVC with specific voulme. 
   ```yaml
	# namespace-pvc.yaml
	apiVersion: v1
	kind: Namespace
	metadata:
	  name: testing
	--- 
	apiVersion: v1
	kind: PersistentVolumeClaim
	metadata:
	  name: testing-pvc
	  namespace: testing 
	spec:
	  storageClassName: rook-cephfs
	  accessModes:
	    - ReadWriteMany
	  resources:
	    requests:
	      storage: 100Mi   
	```

2. Create Pod which claim the previous PVC. 
   ```yaml
	apiVersion: v1
	kind: Pod
	metadata:
	  name: ubuntu-pod
	  namespace: testing
	spec:
	  containers:
	  - name: ubuntu
	    image: ubuntu:latest
	    command: ["/bin/bash", "-c", "sleep infinity"]
	    resources:
	      requests:
	        memory: "64Mi"
	        cpu: "250m"
	    volumeMounts:
	    - mountPath: /mnt/my-storage
	      name: cephfs-storage
	  volumes:
	  - name: cephfs-storage
	    persistentVolumeClaim:
	      claimName: testing-pvc
	```


3. Monitor the created PVC 
   ![](pvc-image.png)
4. Create file with the following command 
   ```bash 
	   dd if=/dev/zero of=filename.txt bs=1M count=50
	```
5. Monitor the pvc 
   ![](pve-iamge-after.png)
6. Create alert from the Alert manager in grafana with following query
   ```bash 
	 100.0 * kubelet_volume_stats_used_bytes{namespace="testing", persistentvolumeclaim="testing-pvc"} / kubelet_volume_stats_capacity_bytes{namespace="testing", persistentvolumeclaim="testing-pvc"}
	 ```
	 This is the result 
	 ![](pvcalert.png)

#### Docker Compose 
All need configuration in `labs` repo

## Install Prometheus and grafana on kubernetes with helm 

1. Don't forget to create `PVC` for Grafana to keep all dashboards even if you delete grafana pod.  
2. Add helm repo 
   ```bash
   helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
	helm repo update   
	```

3. Create value file which contain the PVC and some configuration 
   ```yaml
     prometheus:
        prometheusSpec:
          storageSpec:
            volumeClaimTemplate:
              spec:
                storageClassName: rook-cephfs
                accessModes:
                  - ReadWriteOnce
                resources:
                  requests:
                    storage: 100Mi # Change for your need. 
	```

4. Installation 
   ```bash
   helm install prometheus prometheus-community/kube-prometheus-stack -f 01-values.yaml --namespace monitoring   
	```
5. Get Grafana password 
	   ```bash
	   kubectl get secret --namespace monitoring prometheus-grafana -o jsonpath="{.data.admin-password}" | base64 --decode  
		```

6. Create LoadBalancer service type 
   ```yaml
     # 01-loadbalancer-svc.yaml
	apiVersion: v1
	kind: Service
	metadata:
	  annotations:
	    meta.helm.sh/release-name: prometheus
	    meta.helm.sh/release-namespace: monitoring
	  creationTimestamp: "2024-08-05T08:43:32Z"
	  labels:
	    app.kubernetes.io/instance: prometheus
	    app.kubernetes.io/managed-by: Helm
	    app.kubernetes.io/name: grafana
	    app.kubernetes.io/version: 11.1.0
	    helm.sh/chart: grafana-8.3.8
	  name: prometheus-grafana
	  namespace: monitoring
	  resourceVersion: "5289336"
	  uid: e9898010-e7e4-4d74-bc84-677a60dc8928
	spec:
	  type: LoadBalancer
	  ports:
	  - name: http-web
	    port: 80
	    protocol: TCP
	    targetPort: 3000
	  selector:
	    app.kubernetes.io/instance: prometheus
		    app.kubernetes.io/name: grafana
	status:
	  loadBalancer: {}
	 ```