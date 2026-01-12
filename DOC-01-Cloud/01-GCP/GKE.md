
#### Ato scaling 
1. Cluster Autoscaler 
2. Node auto-provisioning 
3. Setting up autoscaling
Auto scaling in GKE is in two dimensions Workload & Infrastructure 
![[auto-scalar.png]]  
- Node Autoscaler based on Pod Scheduling. 
- How it works
	- A pod is created. 
	- the scheduler tries to place it on existing nodes. 
	- If the pod is Pending due to lack of resouces
		- CPU / Memory 
		- Node selectors / taints 
	- Cluster Autoscaler detects Unschedulable Pods 
	- It add new node to the cluster. 
	- The scheduler places the pod on the new node. 