#### Networking 
- Composer-2 components spread across two locations 
	- Customer Project 
		contains (GKE Cluster, Cloud SQL, DAG buckets). Note that GKE nodes and pods ranges use IP address from VPC. 
	- Google Managed Network Contain the core composer backend services. It's connected to your project's VPC via a network connection, typically using either **VPC Peering** or **Private Service Connect (PSC)**
	- 