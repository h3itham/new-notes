#### Introduction 
- <span style="color:rgb(255, 0, 0)"> What is Kubernetes? </span> 
   Container orchestration for: 
	   1. Speed Deployment. 
	   2. Self Healing 
	   3. Auto Scaling 
		![[DOC-04-Kubernetes/img/kubernetesbefints.png]]
- <span style="color:rgb(255, 0, 0)"> What is pod? </span>
  Group of one or more than one containers which share the same resources (Storage, network, ....), Pods are always run on a single worker node it never spans multiple worker nodes. 
- Kubernetes Architecture 
  ![](k8sarche.png)
	 - **API Server**: Entry point for k8s cluster, which validate and processed all Restfaull requests to cluster. 
	 - **Scheduler:** Assign  pods to nodes. 
	 - **ETCD**: Cluster Database. 
	 - **Controller** **manager**: compare cluster current state with desired state. 
	 - **Cloud Controller Manager:** integrate with specific cloud provider.  
#### General Notes 
- There are different scaling requirement in fronted and backend so you should put them in different pods, Databases are usually much harder to scale. 
- Note that Deployment for stateless apps and statefulset for statefull Apps such databases. 
- <span style="color:rgb(255, 0, 0)">What is High availability mean? </span>
   No Downtime.  
- <span style="color:rgb(255, 0, 0)">what is mean of scalability auto scaling? </span>
   Ability to handle traffic (Up, Down).
- <span style="color:rgb(255, 0, 0)">What is the different between mutable and immutable infrastructure? </span>
   1. **Mutable** (قابلة للتغير ) changes are applied as<span style="color:rgb(0, 176, 240)"> incremental update</span> to an existing system. Example upgrade or update system with `apt update` command. 
   2. **Immutable** (غير قابلة للتغير ) changes replace the <span style="color:rgb(0, 176, 240)">entire system.</span> example docker container or running software.  
- <span style="color:rgb(255, 0, 0)">why we need pods?, why ca't we use containers directly? </span>
  1. Container are used for isolation principle so container should be contain one and only one application.   
 2. Because of that we need <span style="color:rgb(255, 255, 0)">higher-level construct</span> which can contain more than of container with different functions.  
    ![](whypod.png)

- <span style="color:rgb(255, 0, 0)">If you have application which divide in frontend and Backend can you run them in the same pod? </span>
	Of course we can. but there another problem will raise (Resource waste). If you have a two node kubernets cluster and only there is asingle pod, you will only be using a single worker node and not taking advantage of the computional resouces (CPU and Memory) you have at your dispoasal on the second node. spliting the pod inot two would allow kubernetes to schedule the frontend to one node and the backend to the other node. 
- When you decide whether to put two conatiners into a single pod or into two separated pods, you always need to ask yourself the following questions. 
	1. Do They need to be run together or can they run on different hosts. 
	2. Do they represent a single whole or are they independent components? 
	3. Must they be scaled together or individually? 
- Service: is network abstraction over a set of pods. 
- Etcd stored in file path in master node in the following path `/var/lib/etcd`. 
- <span style="color:rgb(255, 0, 0)">Why kubernetes need servcie? </span>
   In General servers, mahcines IPs are defined with sysAdmins, but in k8s there is another philosophy because
	   1. Pods are emphemral (come and go)
	   2. Kubernetes assign IP address to pod after the pod has been scheduled to run on a node. 
	   3. Auto scaling mean that a lot pods are created without knowing it's IPs. 
#### Types of services 
1. <span style="color:rgb(0, 176, 240)">clusterIP</span> "default service" 
   - Role: Internal communication within the cluster. 
   - How it works: assign unique virtual IP address to a set of pods, to allow them to communicate inside a cluster. 
   - Practical use: ideal for backend service where direct access from exteranl sources isn't necessary. 
   - Only accessible from withing kubernetes cluster. 
2. <span style="color:rgb(0, 176, 240)">NodePort</span>
   - Role: Expose the service on All nodes isnide the cluter with the same static port [30000-32767] node's IP addresss at a static port. 
   - How it works: in addition to clusterIP functioanlity, it also opens a specific port on each node. This allows external traffric to reach the service throught the node's IP address and the chosen port. 
3. <span style="color:rgb(0, 176, 240)">LoadBalancer</span>
   - Role: Make the service externally accessible through a cloud provider's load balancer. 
   - How it works: in addition to NodePort, this service provides a cloud load balancer's IP addres, which then. 
   - Practcial use: Especially usefull in cloud settings for a public-facing. production deployment. 
1. <span style="color:rgb(0, 176, 240)">Headless service</span>
   - Client want to talk to pod directly with it's IP address.  
     ![](headless-service.png)

	- Set clusterIP to "none" which returns Pod IP address instead. 
	  ![](headlessService.png)
     
5. <span style="color:rgb(0, 176, 240)">External Name service</span> 
	- Allow you map kubernetes service to external DNS may be (Database, API). 
	- Route traffic to endpoints out side kubernets cluster. 
	- Example 
	  ```yml 
	apiVersion: v1
	kind: Service
	metadata:
	  name: my-external-service
	spec:
	  type: ExternalName
	  externalName: my.database.example.com

       ```
#### ingres
- Ingress is entry point to kubernetes cluster. 
- Ingress exposes HTTP and HTTPS routes from the cluster service with the cluster. Traffic routing is controlled by rules defined on the ingress resources. 
- Allow route HTTP and HTTPs traffic entering the cluster through a single entry point to different servcies inside the cluster. 
- It is not a service <span style="color:rgb(255, 0, 0)">what is it</span> ? it is <span style="color:rgb(255, 255, 0)">Entry Point</span> for your cluster. 
- Allow you to access kubernetes servcie from outside the kuberetes cluster. by configure access by creating a collection of rules that define which inbound connection reach which servcie. 
- Ingress rules: set of rules for processing inbound HTTP traffic to kubernetees cluster 
	  ![](ingress.png) 

- Configuration 
  ![](ingressimage.png)
- why we prefere or should use Ingress rather than LoadBalancer? 
	1. Each Loadbalancer service require it's own public IP address so for every workload (application)
	   **But** you need public IP address with ingress you can route traffic for more than one servcie with just one IP address but with different paths (URI or sub domains)
	   ![](ingresspower.png)
	   1. Ingress Operation in Application layer (http) and can provide a lot of feature such as 
	      cockie-based sessions affinity. 
	  2. Loadbalancer work in Network Layer. 
	- <span style="color:rgb(255, 0, 0)">What is the purpose of Ingress controller (VOIS)? </span>
		- Routing External traffic To corresponding service in the cluster. 
		- Load Balancing. 
		- TLS/SSL Termination. 
#### API Gateway 
- Entry point to cluster. 
- Provide more features than ingress. 
#### Logs 
- Docker container logs was seen with the following command 
  ```bash 
    docker logs -f [container_name]
	```
- Of course you can use the same scenario in kubernetes you can ssh to node which your pod run and use the same command used in docker. but you should use the following command
  ```bash 
	  kubectl logs [podname]
	  kubectl logs [podname] -c [containername]
	```
	Note that you can only retrieve container logs of pods that are still in existence. to make a pod's log availabe even after the pod is deleted. you need to set up centralized, cluster-wide logging, which stores all the logs into the center store. 

#### Basic Kubectl commands 
kubectl allows us to manange local kubernetes clusters like the minikube cluster or remote clusters deployed in the cloud. 
kubectl syntax structure 

kubectl + verb [ create, delete, get descrip, scale, lable] + name [pod, deployment replica set]

I'm using `k9s` to manage all my clusters but I want memorize some commands 
 - To verfiy deployment status 
   ```bash 
	   kubectl get all 
	   kubectl get deployment 
	   kubectl get pods -o wide 
	```

- Scale up and down 
  ```bash 
    kubectl scale deployment [nginx-app] --replica=7
    kubectl scale deployment [nginx-app] --replica=3
```
#### Name space 
- In general is<span style="color:rgb(0, 176, 240)"> Linux Kernel feature</span> which used in process Isolation, But in kubernetes used to group and isolated resouces in different environment. (Dev, Test & production). 
- Deleting a name space will delete all it's child objects. 
- Advantages of using namesapces 
	1. Structure your component.  
	2. Avoid conflicts between teams. 
	3. Access and resources limits. 
- Change active name sapce 
  There is an tool which name is `kubens`
  ```bash 
	  kubens [name_space]
	```
#### Affinity, Anti affinity, Taint (Node), Toleration (Toleration). 

**Affinity** Always ** mean (<span style="color:rgb(0, 176, 240)">Pod Placement</span>) which divide to three types 
1. *Node affinity*: 
   Every Node has it's own labels & Pod scheduled Based on specific node labels. (Node Attract Pod)
2. *Pod affinity*: 
    Schedule related pods to be on nearby nodes. 
3. *Session affinnit*y: sticky session to make ensure that the same request will be served with the same pod. 

**Anti affinity**
make sure that specific pods Don't be scheduled in the same nodes. 
**Taint (nodes)**
Mechanism applied to nodes to restrict which pods can scheduled on them. unless pods explicitly tolerate them.  

**Toleration (Pod)** 
Allow pod to be scheduled to specific nodes. 

#### Services 
- Is Network abstraction.  
- resource you create to make a single, constant point of entry to group of pods. 
- is a way to Expose application running on a set of pods to network. 
- lifecycle of pods and servcie <span style="color:rgb(0, 176, 240)">Not connected </span>
- Service are useful because pods <span style="color:rgb(0, 176, 240)">are ephemeral </span>
- Differentiate betweeen `port` and `targetPort`
	  **Port**: Service port  
	  **targetPort:** pod port. 
#### Tips & tricks 
- create alias 
  ```bash 
  alias k='kubectl'
	```
- Configure tab completion add the following to `.bashrc` file 
  ```bash
    source < (kubectl completion bash) 
    source < (kubectl completion bash | sed s/kubectl/k/g)
    ```

- Install k9s 
 ```bash
 curl -Lo k9s_Linux_amd64.tar.gz https://github.com/derailed/k9s/releases/latest/download/k9s_Linux_amd64.tar.gz
tar -xzf k9s_Linux_amd64.tar.gz 
sudo mv k9s /usr/local/bin/


 ```
  

#### Service Account & RBAC
- Identity used by pods (Applications) to interact with API server. 
- Every pod automatically gets default service account. 
- RBAC: Resources Based Access Control. 
##### Use Cases
1. Monitoring Pod(Prometheus)
	Need permission to read metrics from cluster assign it a service account with `get/list` permissions.
2. CI/CD pipelines 
	pod that deploys apps → assign a service account with `create/update` permissions on deployments.

## Volumes & Storage

#### Storage architecture requirement.
- Storage "Volume" is not depend on the pod life cycle.
- Storage must be available for all nodes.
- Storage needs to survive even if cluster crash. [IMP]
#### Volumes in side pod
- Share the same lifecycle as the pod.
- Volume's content will be persist while containers restart inside pods.
- Volumes Types:
    1. empytDir Simple empty folder.
    2. hostPath Data from worker nodes file system.
    3. gitRepo check data from github repository.
    4. NFS Network file system
    5. BlockStore from amazon 
    and there are a lot of types that are used for certain scenario
- Note that "configMap, Secrete" are special types of volumes.
#### Persistent volume (PV)
- <span style="color:rgb(0, 255, 0)">Interface</span> between your Kubernetes objects and actual storage. [IMP]
- Abstract for Physical or Networked storage in kubernetes cluster.
- PV is outside of the namespace.
- Access control over data py define access mode (ReadWriteOnce, ReadOlnlyMany, ReadWriteMany)
#### Access modes
To understand it carefully I created a scenario. You have three nodes let say node1, node2, node3.
1. ReadWriteOnce
    Volume can only be mounted as read-write by one node at a time. (No Concurrently access ) and of course all pods on different nodes will access it but in different times.
2. ReadOnlyMany
    Allows a Persistent volume (PV) to be mounted as read only by multiple nodes.
3. ReadWriteMany
    allows a Persistent Volume (PV) to be mounted as read-write by multiple nodes simultaneously.
#### Persistent Volume Claim (PVC)
- **Request** for storage by pods.
- Used by developer to specify they storage requirement.
- while pod is deleted the associated pvc and data it contains are not immediately deleted.

##### **Example**

1. create Persistent volume
    
    ```yaml
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: local-pv
    spec:
      capacity:
        storage: 500Mi 
      volumeMode: Filesystem
      accessModes:
        - ReadWriteOnce
      persistentVolumeReclaimPolicy: Retain
      storageClassName: local-storage
      hostPath:
        path: /mnt/pv-data
    ```
    
2. Apply and Ensure that pv is created
    
    ```bash
    k apply -f pv.yaml 
    k get pv
    ```
    
3. Create Persistent Volume claim.
    
    ```yaml
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: my-pvc
    spec:
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 200Mi 
      storageClassName: local-storage
    ```
    
4. Apply and ensure that pvc is created
    
    ```bash
    k apply -f pvc.yaml 
    k get pvc
    ```
    
5. Create Nginx deployment that use this
    
    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nginx-deployment
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: nginx
      template:
        metadata:
          labels:
            app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx:latest
            ports:
            - containerPort: 80
            volumeMounts:
            - name: nginx-persistent-storage
              mountPath: /usr/share/nginx/html
          volumes:
          - name: nginx-persistent-storage
            persistentVolumeClaim:
              claimName: my-pvc
    ```
    
6. Apply deployment
    
    ```bash
    kubeclt apply -f nginx-deployment.yaml
    ```
    
7. Try to create some content on specific `/usr/share/nginx/htm/` in the deployment and after that delete deployment and create another one and recheck the data.
    
    ```bash
    kubectl exec -it deployment-84cff9d74d-m5fjz -- /bin/bash
    cd /usr/share/nginx/html
    ```