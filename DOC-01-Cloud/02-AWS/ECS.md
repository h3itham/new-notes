
#### Orchestrators 
- Manage lifecycle of containers (create, update, delete)
- Autoscaling to handle variance in traffic
- Rolling out changes to application
##### Introduction 
- Elastic Container Service 
- There was problems with virtualization one of them is that the VMs take a big amount of resources. 
	![[storageproblem.png]]
#### ECS Concepts 
- ECS is Cloud based and fully managed container orchestration service. 
- ECS supports integration with several useful AWS services and features such as IAM, EBS, ELB,.......
- ECS eliminates the need to set up and maintain the infrastructure of Kubernetes clusters by taking responsibility over these tasks.
- It Comes with two launch  types 
	   1. Fargate Launch type: Delegate the management of virtual machines and servers to AWS.  
	   2. ECS launch type: EC2 instance + ECS Agent (Server-based) 
#### terminologies 
- **Cluster**: 
	-  Logical grouping of resources (EC2 instances or Fargate)
	- Your ECS "workspace"
- **Task definition** 
	- Blueprint for your application. 
	- Defines: container images, CPU/memory, networking, volumes, environment variables
	- JSON template that describes one or more containers 
- **Tasks** 
	- Running Instance from task definition. 
- **Service** 
	- Manage Long running Tasks 
	-  Maintains desired number of tasks
	-  Handles load balancing, auto-scaling, deployments
	- Ensures your app stays running


- **Task**: running container with the settings defined in the Task Definition. It can be thought of as an “instance” of a Task Definition.
