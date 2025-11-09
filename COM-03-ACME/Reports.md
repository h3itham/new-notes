#### JAN 
1. Week "1"  12-1-2025

    - **Past week Tasks**
	    - VACATION 
	- **This weeks's plan**
		- Collaborate with developers to understand the latest changes in the code
		- Deploy the new version from Node (websocket). 
		-  Define backup strategies for application and user data outside the Kubernetes cluster. 
		- Create Custom Grafana Dashboard that contain [ Nodes memory (RAM), Cluster CPU, PVC sizes.  ]


2. Week "2" 
	- **Past week Tasks**
		- Created new Development environment (Sail machine). 
		- Deployed new version from Node 
		- Created custom Grafana Dashboa
	- This week's plan 
		- Replace sail with Docker compose for seamless configuration. 
		- Install Redis insight to manage redis 
		- Define backup strategies for application and user data outside the Kubernetes cluster. (Pending form last week "need your approval"). 

3. Week "3" 
	 - **Past week Tasks**
		 - Installed Redis insight. 
		 - Solve Certificate Problems in production Environment. 
		 - Configure communication server to work with "HAproxy" for SSL termination (Dev Environment) .
		 - Applied simple DNS poc on (Dev Environment) 
	- **This week's plan**
		- Complete DNS poc. 

4. Week "4"
	- **Past week Tasks**
		- Complete DNS poc 
		- Deployed the last version from communication server (socket).
		- Replaced `HAproxy-ingress controller` with `HAproxy Loadbalancer`
	- **This week's plan**
		- Collecting Communication server logs with Elastic Search.
		- Create staging environment. (Need your action)

4. Week "4"
	- **Past week Tasks**
		- Replaced haproxy with Nginx-ingress controller for sticky session. 
		- Redployed ceph, metalLB, nginx-ingress, backend, communication server,  and redisth in the new cluster. 
		- Fixed pipelines issues related to Dockerfile  
	- **Next week plan** 
		- Redeploy and configure Elastic Search. 
		- Redeploy and configure Prometheus and grafana 

5. week "5"
	- **Past week Tasks**
		- Deployed and configure Elastic Search 
		- Deployed and configure Prometheus and grafana 
		- Configured `PHP-FPM` for Portal deployment.
		- Conducted a meeting with Islam and facilitated the testing of the new environment with multi-networking (APN and Ethernet) on Vodafone Hosting.
	- **This week's plan.** 
		- Setup NFS server for subscription offices.

6. week "6"
	- **Past Week's Tasks:**
	    - Created all dependencies needed for the subscriptions office (NFS taken directly from the company’s NFS server, database created).
	    - Generated a self-signed certificate using Cert Manager (Awaiting validation).
	- **This Week's Plan:**
	    - Ensure that the production Kubernetes cluster can resolve external DNS.
	    - Collaborate with our teams to define access policies for Kubernetes clusters.

7. week "7"
	- **Past Week's Tasks:**
		- Implemented all required changes for the new communication server, including DNS resolution, Redis, and Ingress-Nginx.
	    - Deployed the latest version of backend in the production. 
	    - Fixed a critical Redis issue in production.
	- **This Week's Plan:**
	    - Review and enhance Kubernetes project-related configurations.


8. week "8"
	- **Past Week's Tasks:**
		- Added CI/CD to the master branch and configured it to run with image tags instead of commits.
		- Used socat to allow the development teams to access hosting databases through the company VPN.
	    - Reviewed and enhanced Kubernetes project-related configurations.
	- **This Week's Plan:**
	    - Working on Kubernetes backup and restore using Velero and MinIO.


9. week "9"
	- **Past Week's Tasks:**
		- Fixed time out issue from production. 
	    - Reviewed and enhanced Kubernetes project-related configurations.
	- **This Week's Plan:**
	    - Working on Kubernetes backup and restore using Velero and MinIO.
10. week "10"
	- Past week's Tasks: 
		  - With Mohamed talal we done demo for velero backup and restore. 
		  - Prepare the documentation structure the latest changes you can see them at the `documentation` repo in gitlab "link"
	- This week's plan 
		- start handover plan offcourse if you approved it. 
11. week "11"
     - Hand over
12. week "12"
	-  Hand over. 

