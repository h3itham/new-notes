#### Introduction 
- Why I learn GCP? 
	1. Business needs (VOIS). 
	2. Strong Big Data and machine learning capalities. 
#### General 
- Google cloud resource hierarchy 
	![[google-hierarchy.png]]
	- The resource hierarchy directly relates to how policies are managed and applied on Google Cloud
- Every Resource In GCP has it's own scope:-  
	- Global Resource. 
	- Regional Resource. 
	- Zonal Resource. 
- Google Cloud APIs 
	- Interfaces that allow interaction with google cloud services programmatically. 
	- Each Clould API use `googleapi.com` domain such as `compute.googleapi.com, storage.googleapi.com`

#### Networking
##### VPC
- Logically isolated Network on GCP. 
- Provide a Network functionality for 
	- Compute Engine Machines (VMs). 
	- GKE clusters 
	- Serverlesss workloads 
- Every VPC has two implied firewall rules that block all incoming connections and allow all outgoing connections.  
- VPC-Service Control (SC): Is security feature that mitigate data risks by creating permitter around the service. 
- VPC-SC Created on Organization Level. 
- Subnets are regional resources. 
- **VPC Types** 
	1. Default VPC: Automatically created once compute engine API enabled in GCP project. 
	2. Auto mode VPC:  
	3. Custom VPC : <span style="color:rgb(255, 0, 0)">Recommended VPC</span> 
- **IPs** 
	- Subnet Address range `10.0.2.0/24` 
		1. <span style="color:rgb(255, 255, 0)">Network Address</span> `10.0.2.0`
		2. Default GW Address `10.0.2.1`
		3. Future use Address `10.0.2.254`
		4. <span style="color:rgb(255, 255, 0)">BroadCast Address</span> `10.0.2.255`
	- IP Types 
		1. Ephemeral Automatic Internal IP. 
		2. Ephemeral Custom Internal IP 
		3. Static Reserved internal IP 
- You can Expand CIDR ranges without downtime. 
- Resources inside the same VPC can communicate with the internal IPs. 
##### VPC Routing 
- Define how traffic is directed between resources in `gcp-VPC` to internet, on-premsis or even another VPC. 
- Each VPC has global routing table (System Generated - Custom Routes). 
- Routing priorities (Lower value = Higher Priority)
- UseCases 
	- Connect multiple subnets across regions.
	- Direct traffic through security appliances (firewalls/proxies).
	- Control hybrid cloud (VPN/Interconnect) traffic.
	- Multi-VPC network peering and routing.
		![[vpc-routing.png]]
- VPC Implied Firewall 
	- Are default firewall that automatically exist in every VPC network. 
		![[gcp-vpc-implied-rules.png]]
	 
##### Routing 
- Determine traffic flow between  
	1. VMs
	2. subnets 
	3. On-premises network 
	4. The internet 
##### Firewall & Firewall Rules 
- Defined on VPC level but applied on instance Level 
- Rule component 
	1. Priority (Lower number = Higher priority)
	2. Action (Allow or Deny)
	3. Direction (ingress or egress)
	4. Source or Destination (IP range or tags)
	5. Protocol and ports
	6. Target Resouces

###### Firewall Policies  
###### VPC firewall rules 
- Applied to project and network
- Allow or deny connection to and from VMs
- Firewall rules defined at network level 
  
##### VPC Peering 
- Communication between two VPCs <span style="color:rgb(0, 0, 255)">through</span> GCP internal network. 
- Automatically exchage the route tables between VPCs. 
- Reduce Network Latency. 
- increases network security. 
- Reduce network costs (Egress Data)
- You created Peering configuration in both VPC. 
##### Shared VPC  (Common VPC)
- Share a VPC network from one project (Host project) to another projects in your GCP organization. 
- Allow organization to connect resources from multiple projects to a common VPC Network. 
- Resources take <span style="color:rgb(0, 255, 0)">internal</span> IPs from shared VPC. 
		![[sharedvpc.png]]
##### VPC Flow Logs 
- Record Network traffic going to and from VM network interface. 
- Use cases 
	1. Troubleshooting Network issues. 
	2. Monitoring security 
	3. Forensics and traffic analysis. 
- How VPC flow logs work? 
	- Enabled Per subnet. 
	- Logs exported to cloud logging for 30 days 
	- Can be saved at GCS bucket. 
- How can you enable it? 
	1. Go to **VPC Network** → **Subnets**
	2. Select your subnet → **Edit**
	3. Scroll down to **Flow logs** → Set to `On`
#### DNS 
- DNS is a global, Distributed & decentralized database which contain inforamtion about Domain Name and it's IP address. 
- DNS Structure 
		![[dnsStructure.png]]
##### Cloud NAT 
- NAT: Network Address Translation 
- Allow private Compute Engine VMs or GKE pods, which only have private IP addresses. to access the internet for outbound traffic 
- Map internal Private IPs to public IP address pool managed by cloud NAT. 
- Use Cases 
	- Secure Internet Access for private resources. 
	- GKE Clusters 
	- Security 
#### LoadBalancing 
- Distribute traffic 

#### IAM 
- Identity and Access Management 
- Define <span style="color:rgb(255, 0, 0)">who</span> can do <span style="color:rgb(255, 0, 0)">what</span> on <span style="color:rgb(255, 0, 0)">which</span> resources. 
- Least Privilege: bare minimum privileges to (User, Program, SA)
- Identity = principal in gcp 
		![[principles.png]]
- Role: Collection of permissions 
- Role binding: Permissions assigned to principal 
	![[iamoverall.png]]
- IAM Component 
	1. Principal 
	2. Role: collection of permissions 
	3. Resource 
- You can't grant permission to user, You grant role. 
- IAM Role (principal)
	1. Human Resources 
		- Users
		- Groups
	2. Workloads 
		- Service account 
		- Cloud identity domain
- IAM permission Representaion 
	```
	service.resource.verb 
	```
- Roles Types 
	1. Basic Roles (Permitive) 
		- These are the predefined roles that existed before IAM was introduced.
		- offer **broad permissions** across the project and are generally not recommended due to their lack of granularity.
	2. Predefined Roles
		- Created with Google and are designed to align with common job functions. 
		- more secure 
	3. Custom Roles 
		- Custom roles allow you to create roles with a specific set of permissions tailored to your organization's needs.
		- Useful when predefined roles don't meet your requirements. 
		- Require careful management to avoid granting unnecessary permissions. 
			- Start stop compute engine 
 ##### **Service Account** 
- <span style="color:rgb(0, 255, 0)">special type of account</span> that represents an application or compute workload, <span style="color:rgb(255, 255, 0)">not a human user</span>.
- Used when GCP service need to talk with another GCP service. 
- types 
	- User Managed 
		- You create it. 
		- Youn can Assign custom roles, generate keys 
	- Google Managed 
		- Automatically created with Google when you use certain service. 
	- Impersonated service account 
- Service Account keys 
	- JSON file which allow an external system(Script, app, CI/CD) to authenticate as a service account to access GCP Resources. 
	- Contains 
		- `client_email`
		- `private_key`
		- `project_id`
- SA Impersonation 
	- Allow user, script or another Service Account to <span style="color:rgb(255, 0, 0)">act</span> as a different Service Account. 
	- Use Cases 
		- `CI/CD` Pipelines
		- Cross Project Access. 
		- terraform
	- Example: You have two Service Account 
		- **A** → `sa-a@project-id.iam.gserviceaccount.com` (Target, owns resources)
		- **B** → `sa-b@project-id.iam.gserviceaccount.com` (Caller, wants to impersonate A)
		- Steps 
			1. Grant Permission Rights or give sa-B permission to impersonate sa-B 
				```bash 
				gcloud iam service-accounts add-iam-policy-binding \
				  sa-a@project-id.iam.gserviceaccount.com \
				  --member="serviceAccount:sa-b@project-id.iam.gserviceaccount.com" \
				  --role="roles/iam.serviceAccountTokenCreator"
				
			```
			2. Impersonate A using B 
				```bash 
				# Authenticate A
				gcloud auth activate-service-account sa-b@project-id.iam.gserviceaccount.com \
				  --key-file=sa-b-key.json
				# Impersonate 
				gcloud config set auth/impersonate_service_account sa-a@project-id.iam.gserviceaccount.com
			```

- **Service Agent**: Special type of SA, auto created and managed by Google to run platform managed servies. Created when you enable service API and Deleted when you delete it. 
- IAM Hierarch 
	- Organization Level
	- Folder Level 
	- Project Level 
	- Resource Level 
##### workload identity 
- is a way to authenticate application running on kubernetes with GCP service. 
- Add Authentication to pods in kubernetes cluster. 
#### Identity Aware Proxy (IAP) 
- Control access based on
	- User Identity. 
	- Context. 
- OAuth: Open Authentication 
- Every Request evaluated in real time. 
- Use Cases 
	- Provide Centeral authentication and authorization for your application. 
	- secure web application and APIs
	- Secure SSH Connection 
	- Replace VPN. 
#### Cloud Identity 
- Allow you to manage and authenticate users for your cloud based applications and resource. 
- Provide (Single singe on, Multi Factor Authentication, Access Control)
#### Secret Manager 
- Store, Managed secrets as binary, blobs or text strings. 
- Why we don't use KMS? 
	KMS doesn't store secrets it only have keys to encrypt/decrypt data. 
	Secret Manager also store multiple version from key. 
- What is the difference between KMS and Secret Manager? 
	- KMS Dose not store secrets it just store keys 
	- Secret Manager 
		- Store secrets 
		- Keep history of secrets version 
##### Privileged Access Management (PAM). 
- Grant temporary access to specific users and automatically revoke after set of duration. 
#### Tools 
##### Cloud Shell
- Full managed browser based command line interface. 
- Contain a lot of preinstalled tools (gcloud, Kubectl, docker, terrafrom). 
##### gcloud 
- Google CLI to manage GCP from terminal. 
- General Structure
	```bash 
	gcloud [GROUP] [COMMANDS] [FLAGS]
	```
- Practice common scenarios 
	1. Authentication 
		```bash 
		gcloud auth login 
		```
	2. Project 
		```bash 
		gcloud config set project
		```
	3. Compute Engine 
		1. Create VM 
			```bash 
			gcloud compute instances create my-vm \
			  --zone=us-central1-a \
			  --machine-type=e2-medium \
			  --image-family=debian-11 \
			  --image-project=debian-cloud
			
			```
		2. Delete VM 
			```bash 
			gcloud compute instances delete my-vm \
			  --zone=us-central1-a
			```
		3. List VMs
			```bash 
			gcloud compute instances list 
			```
	4. Storage 
		1. Upload object 
			```bash 
			gsutil cp [localdir/filePath] [ gs://bucketName]
			```
		2. Download object 
			```bash 
			gsutil cp [gs://bucketName] [localdir]
			```
		3. List bucket 
			```bash 
			gsutil ls gs://bucketname 
			```
- Installations 
	```bash 
	echo "deb [signed-by=/usr/share/keyrings/cloud.google.gpg] https://packages.cloud.google.com/apt cloud-sdk main" | sudo tee -a /etc/apt/sources.list.d/google-cloud-sdk.list
	
	# Add GPG Key 
	curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key --keyring /usr/share/keyrings/cloud.google.gpg add -
	
	# Update Repos and installation
	sudo apt-get update
	sudo apt-get install google-cloud-cli
	```
- Auto completion 
	```bash 
	echo "source /usr/share/google-cloud-sdk/completion.bash.inc" >> ~/.bashrc
	source ~/.bashrc
	```
- Authentication and configurations 
	1. Login 
		```bash 
		gcloud auth login
		```
	2. Set your default project 
		```bash 
		gcloud config set project [project_ID]
		```
	3. Check your current configuration 
		```bash 
		gcloud config list 
		```
- The basic common command you should know 
	1. List all projects in your account 
		```bash 
		gcloud projects list
		```
	2. List all instances 
		```bash 
		 gcloud compute instances list
	   ```
#### Compute 
##### LoadBalancers 
- LoadBalancer distribute incoming traffic across multiple targets
- Fully distributed and software defined. 
- LoadBalancers types 
	1. HTTP(S) Load Balancing
	2. TCP/UDP Load Balancing
	3.  Internal Load Balancing
	- To decide which loadbalancer you want refer to the following 
		![[loadbalancingtype.png]]
- Termnologies 
	1. Backend Service
		- Define how incoming traffic is distributed and managed with GCP loadbalancer. 
		- Sets of backend instances or service that receive traffic from LoadBalancer. 
	2. Backend types 
		1. Instance Group
		2. Zonal NEGs (Network Endpoint Groups)
		3. Serverless NEGs
		4. Backend Buckets
	3. Health check 
		- Monitor the status of backend instances or service to ensure they are healthy and capable of handling incoming traffic.  
	4. Session affinity (stiky session) 
		- The same client will be served with the same server. 
	5. Service timeout 
		- Maximum time which LoadBalancer wait backend to respond. 
	6. Traffic Distribution 
		- Algorithm used by the laodbalancer (Round Robin, Least Connection,...)
##### High Availability & Autoscaling 
- **Instance Groups**
	- Collection of VM instances that you can managed
	- Types 
		1. Managed Instance Groups (MIGs)
			- use cases 
				1. Stateless applications (Frontend,....)
				2. Stateless patch or high throughput compute workloads (image processing)
						![[manageinstances.png]]
		2. UnManaged Instance Groups 
- **Instance Template** 
	- Designed to create VMs with identical configuration

#### Storage & Databases 
 There are different types of storage, each designed for different specific use cases and workloads. 
##### Cloud Storage OR Google Cloud Storage (GCS) 
- Object storage designed for store and retrieve unstructured data. 
- Use cases (Data archiving, Content Delivery, backup and restore). 
- Storage Classes 
	1. *Standard*: High performance, low-latency storage for frequently Asked data. 
	2. *Nearline*: Low-cost storage for data accessed less than once a month. 
	3. *coldline*: very low-cost storage for data accessed less than once a quarter. 
	4. *Archive*: Lowest-cost storage for data accessed less than once a year. 
		![[cloudstorage.png]]

- Encryption  
- You need to see the [Envelope Encryption Concept](https://www.youtube.com/watch?v=D1mjSJQ86lE&list=PLUMFBjy3l_-p4DMPYHsdzr_Jeu85uc0Bl&ab_channel=MazenBassiouny)
	- Transit (TLS, VPN tunnel )
	- Rest  ( Database, Files)
		- Client Side 
		- Server Side 

##### Cloud SQL (Managed Relational Database Storage)
- Cloud SQL is a fully managed relational database service that supports MySQL.  
- Use cases (Web Applications, Transactional systems). 
- Pricing is based on machine type (CPU/memory), storage amount, and network usage.
- To connect to database 
	- Public IP (you should add your ip "client IP" in authorized network). 
	- Priavte IP
- Connect from GKE cluster to cloud sql 
	there are many methods to do that. but the best practice say that you should use `cloud SQL proxy`. 
	Note that GKE nodes assigned to default compute engine service account which has high privilege 
	![[cloudsql-gke.png]]
	The application container remains agnoistic of how to connect to cloud sql. the proxy handel all connection details. 
##### Cloud Spanner (distributed) (Relational Database with horizontal scaling )
- Distributed relational database. 
- Uses Cases Global applications requiring strong consistency and horizontal scalability.
- Pricing involves node costs (compute capacity), storage costs, and network egress
##### Cloud Bigtable (Nosql wide-column storage)
- Cloud Bigtable is a fully managed, scalable NoSQL database designed for large analytical and operational workloads.
- Use cases: Time series Data, IOT and big data analytics 
##### BigQuery 
[[Google Data Engineering Course#BigQuery (Storage & analytics)]]
#### Monitoring and Logging 
- First you should refer to the following doc  [[Observability]] 
- Monitor is the foundation of product Reliability 
- Logging consist from the following
	- Storage for logs. 
	- Interface "Log Explorer"
- Logging services allows you 
	1. Read and Write Logs. 
	2. Query Logs 
	3. Control How route and store logs. 
1. Logs have limited time which is "Retention Period"
##### Cloud Monitoring 
- Collect metrics, events, and metadata 
- Provide visibility into performance uptime
- Ingest metics and provide dashboard 
##### Cloud Logging 
- Allows users to collect, store search analyze monitor log and events. 
- Logging has multiple aspects 
	- Collect Logs 
	- Analyze
	- Export: Logs to Cloud Storage, BigQuery.  
	- Retain: Default 30 days 
##### Error Reporting 
- Identifies, counts, analyzes and aggregates the crashes in your running cloud services. 
##### Application Performance Management 
- Clou Trace 
#### Cloud Armor (WAF)
- Protect your Google Cloud deployments from multiple types of threats: 
	- Distributed Denial Of Service (DDOS)
	- Cross-site scripting (xss)
	- SQL injection(SQLI)
	![[cloudarmor.png]]
- How Cloud Armor work? 
	1. Integrate with Google Loadbalancer. 
	2. Create Policies which are combination of  (IP address, geolocation & attribute of request)
#### Cloud Build
- is a CI/CD platform that let you build, test, Deploy your application. 
- Config file name `cloudbuild.yaml`
- Basic Flow 
	1. Trigger pipeline 
	2. Build Steps 
	3. Artifacts pushed to registry. 
	4. Deploy to (GKE, Cloud Run, App Engine)
####  Cloud Run (Container as a Service)
- Allow you to run containerized applications. 
- Charge you only on the resources you use. 
- Designed for stateless applications. 
- Types of service 
	1. *Cloud Run services:* Used to run code that responds to web requests, events, or functions.
			![[cloudrunservice.png | 400]]
	2. *Cloud Run jobs:* Used to run code that performs work (a job) and quits when the work is done.
	3. *Cloud Run  worker pools*: Used to run ETL pipelines 
- Scalability 
	1. By Default cloud run has default scalability feature to handle all incoming requests. 
	2. If cloud run dose not receive any traffic, it scale to zero . 
	3. You can change this behavior using [Doc](https://cloud.google.com/run/docs/configuring/min-instances)
- <span style="color:rgb(255, 0, 0)">How can Cloud Run (container) access Specific VPC resourcs?</span> 
	There are two ways to do that 
	1. Direct VPC egress.
		![[directvpcegress.png]]
	2. Serverless VPC Access Connector Architecture (Old)

		ِ![[access-conector.png]]
- Cloud Run use cases 
	- Event Driven Application (Upload file to bucket, pub/sub events)
	- Low traffic applications 
	- APIs or Microservices 
	- Batch Processing 
- Configuration from console. 
	1. Go to Cloud Run service. 
	2. Click on create button. 
	3. Do the following (Name, Region, CPU)
	4. Important staff 
		1. Ingress Control (Interanal: cloud run invoked only from or within the vpc, All: from the internet)
		2. There are different important configuration in Networking and Security tab 
			![[loadbalancer01.png]]
			Such as Session affinity when you are working with websockets. 
			Service Account: which cloud run will use. 
#### Cloud Run Functions 
- Provide execution environment for your code. 
- Serverless compute service 
- Event Driven 
- Pay only for execution time and used resources. 
- Use cases 
	1. Data Processing or ETL (Video Incoding). 
	2. Webhook Respond to HTTP request. 
	3. APIs that compose loosely coupled logic
	4. Mobile backend functions. 
	![[cloudrunfunction.png]]
- Cloudrun Types 
	- Cloudrun service 
		- Request driven (HTTP, gRPC, pub/sub)
		- Pay for request or instance
	- Cloudrun job 
		- Non Request based. 
		- Such Cron Job.  
	- Cloudrun worker pools 
		- Non Request based 
		- used in distributed workload 
- Difference between 1st & 2nd Generations of cloud functions. 
	- 1st Generation: Traditional function-as-a-service, with more limited runtime options, event triggers, and behavior.
	- 2nd Generation: newer version, built on top of **Cloud Run** + **Eventarc** (Fully managed event routine service). More flexible, more features. 

- Service Revision 
	- Consist from container image and config and environment variable. 
	- Each Deployment create revision. 
	- Revision is immutable mean every deployment create revision. 
	- Traffic is routed by default to the latest revision. 
- Service URL 
	- Deterministic 
		```txt
		https://SERVICE_NAME-PROJECT_NUMBER.run.app 
		```
	- Non Deterministic 
		```txt 
		https://SERVICE_IDENTIFIER.run.app
		```
		
#### Cloud Scheduler 
- Fully managed cron job service. 
- Schedule tasks such as 
	- Triggering HTTP EndPoints 
	- Publish message to pubsub 
	- invoke workflows or cloud function 
#### Cloud Run Job (batch Job)
- Not always running as a service run for a task and die. 
- Any task that start --> work --> Stop
#### Pub/sub 
- **Pub/Sub** stands for **Publish/Subscribe** Or <span style="color:rgb(0, 255, 0)">publish message to subscriber</span>. 
- It is a **messaging service** that allows **independent services** to **communicate asynchronously**.
- Built for real-time, reliable, scalable messaging.
- Main Component 
	![[pubsub.png]]
- How it works? 
	1. Publisher sends  a message to a topic. 
	2. Topic holds message. 
	3. subscription deliver message to subscriber 
	4. subscriber process message. 



### Google Kubernetes Engine (GKE). 
- is a managed kubernetes service that lets you deploy, manage and scale containerization application. 
- Node Pools: Group of workers node with the same configuration. 
- There are two modes of GKE 
	- Autopilot: 
		1. Full managed mode where Google handles node management. 
		2. Autopilot GKE is always Regional. 
	- Standard mode
		1. more control over node configuration. 
		2. Deploy as (Zonal, Regional)
##### Kubernetes Access control 
- GKE deals with two categories of access 
	1. **Individual User Accounts.** 
		- Human user or administrator who need to interact with GKE Cluster. 
		- Use IAM to link permissions to user accounts and groups. 
	2. **Kubernetes Service Accounts.** 
		- Identities for **workloads** (Pods), **processes** or **applications** running in the GKE cluster 
		- **Workload Identity** Offer a secure way to authenticate Pods by keeping service account credentials outside the Pods themselves. 
- Pod Security on K8S. 
	- There are two mechanisms for pod security in kubernetes:  
		1. Pod Security Standard (PSS). 
			1. *Privileged* – No restrictions (unsafe).
			2. *Baseline* – Prevents common exploits (default in GKE).
			3. *Restricted* – Strict security (best practices).
		2. Pod Security Admission (PSA). 
			Enforces PSS via namespace labels:
			- **`enforce`**: Blocks violating pods.
			- **`audit`**: Logs violations.
			- **`warn`**: Shows warnings.
##### General 
1. Use the following to create simple production kubernetes cluster 
	```txt
	https://cloud.google.com/kubernetes-engine/docs/tutorials/configure-networking



#### Key Management Services (KMS)
2. How to check your billing in GCP 
	1. Go to the following link 
		```txt 
		https://console.google.com/billing 
		```
	2. Select your billing account. 
	3. In the lift pane select `credits`. 
### Logging & Monitoring 
- Why we need observability in general? 
	- Detect and fix issues. 
	- Monitor system Performance. 
	- Understand system behavior (in real time). 
	- Capacity Planning: Estimate the amount of resources needed by your customer and application. 
- There are four Signals (Golden Signal). 
	![[goldenMetrics.png]]

#### Billing 
- billing Account: Define who pays for a given set of Google Cloud Resources. 
- Multiple Projects are assigned to single Billing Account. 
