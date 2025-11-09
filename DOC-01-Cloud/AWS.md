#### Introduction about AWS and global Infrastructure. 
- **Key Benefits of AWS** 
	- *Scalability*: scale workloads up and down based on demand. 
	- *Cost-effective*: Pay only for what you use. 
	- *Reliability*: Avoid failure as you can. 

- **Global Infrastructure** 
  AWS Operates in Regions, Availability zones, and Edge Locations. 
  
	- Region 
		- Geographically separated areas example (US East, EU Centeral)
		- Each Region has multiple Availability zones. 
		- Choose Region base on Latency, cost and compliance requirements. 
		
	- Availability Zones
		- Isolated data centers within a region. 
		- Designed for fault tolerance. 
		
	- Edge Location 
		- Used by AWS Cloud front (Content Delivery Network). 
		- Cache content closer to users for faster delivery. 

#### Identity and access management (IAM)
- A <span style="color:rgb(255, 0, 0)">framework</span> of policies & processes that ensures the <span style="color:rgb(0, 255, 0)">right individuals</span> (users, systems, services) have the <span style="color:rgb(0, 255, 0)">right access</span> to the <span style="color:rgb(0, 255, 0)">right resources</span>. 
- Key component of IAM 
	1. Identity Management. 
		-  Manages user identities (e.g., employees, customers, devices).
		-  Includes user provisioning (creating accounts), de-provisioning (deleting accounts), and lifecycle management. 
	2. Authentication 
		- verify the identity of users or systems. 
		- Methods Include passwords, multi-factor authentication (MFA). biometrics, and single sign on (SSO). 
	3. Authorization 
		- Determine which resources that user can access. 
	4. Access Control. 
	5. Auditing and compliance 
		- Track logs access events for auditing and compliance purposes. 
	
- IAM Uses Cases
	- Employee Access management. 
	- Customer identity management. 
	- Cloud Security 

- IAM Principle Or identity 
	![](ima-princibles.png)

- Overall IAM
		![](IAM-overall.png)
- <span style="color:rgb(255, 0, 0)">What is the difference between IAM role and IAm policy?</span>
	- To answer this question, let's take a step back. 
	- What is identities 
		- User 
		- Group 
		- Role  <span style="color:rgb(255, 255, 0)">Containers that contain permissions.</span> 
		- Service Account. (Special type for applications)
	- The Role is a IAM identity. which can be authenticate and authorized to aws resources. 
	- IAM policy define Permission of IAM identities (user, Group, role). 
- Here arise another two questions "What is Role & policy?"
	- Role 
		Container that contain  Group of permission 
		1. Trusted Policies. Control who can assume this role. 
		2. Permission policies. Define Allow or Deny specific action. 
			![](IAMRole.png)

	- Policy 
		![](policy.png)
##### Security Token Service (STS)
- Temporary Credentials to specific service. 
#### Elastic Cloud Compute EC2 
- Every instance has one ENI, but in the sam time it has two IPs Public and Private. <span style="color:rgb(255, 0, 0)">Note</span> "You can't see the public IP from the instance it self."
- Amazon Machine Image(AMI). 
	Is the template that contain information and instruction telling EC2 about the Operating system and application software installed. 
- Instance Types
	The idea is to balance costs against your needs (Compute, Memory & CPU).
- EC2 Instance Security 
	AWS Provides four tools to secure EC2 instance. 
	1. Security Group (Control Traffic at instance Level)
		- Play firewall Role. 
		- Deny all incoming traffic. 
		- Allow all outgoing traffic. 
		- <span style="color:rgb(255, 255, 0)">State-full</span> (if one direction allowed second is allowed by default)
	2. Access Control List "ACL" (Control traffic at subnet Level)
		- <span style="color:rgb(255, 255, 0)">State-less</span> 
	3. Identity and access Management(IAM). 
	4. Key pairs. 
#### Auto Scaling Group (ASG)
- AWS feature that automatically adjusts the number or EC2 instance based on demand or predefined conditions. 
- Key features 
	- Scaling Policies 
		- Target Tracking: Maintains a specific metric (e.g., 70% CPU usage).
		- Step scaling Adjusts capacity based on alarm breaches.
		- Simple scaling Adds/removes a fixed number of instances.
	- Health checks - Monitors instance health (EC2 or ELB health checks).
	- cooldwon period which prevent ASG to create or delete new instance until the previous scaling action takes affect. 

- How ASG work 
	1. Launch Template/Configuration:
	   - Defines instance configuration (AMI, instance type, etc.).
	   - Use **Launch Templates** (more flexible) over Launch Configurations.
	2. Desired, Minimum, Maximum Capacity:
	   - Desired: Number of instances to maintain.
	   - Min/Max: Limits for scaling.
	3. Scaling Triggers:
	   - Based on CloudWatch alarms (e.g., CPU, network).
	4. Load Balancer Integration:
	   - Distributes traffic across instances.
#### Elastic Load Balancing ELB. 
- Load Balancer distribute incoming traffic across multiple targets (EC2 instances, containers, IP addrese). 
- Ensure High availability and fault tolerance. 
- Types of Load Balancers in AWS
	1. Application Load Balancer 
		- Layer 7 (HTTP/HTTPs). 
		- Routes traffic based on content (e.g., URL path, hostname). 
		- supports path-based routing and host-based routing. 
	2. Network Load Balancer (NLB)
		- Layer 4 (TCP/UDP)
		- Routes traffic based on IP protocol data. 
		- Handles millions of requests per second. 
		- Ideal for low latency, high throughput apps.
	3. Classic Load Balancer (CLB)
		- Legacy (Layer 4 and Layer 7)
		- Deprecated 

- Consist from three main component 
	1. Listener process that checks for the connections. 
		![](listener.png)
	2. LoadBalancer It self. 
	3. Target group (Instances, containers, Lambda function, and IP address )
		![[targetgroup.png|200]]
	  
- Target Groups Rules 
	Send Traffic to specific target group, Based on Client request. 
	![[reluse.png]]
	
- The difference between Host based and Pass-based 
	![[hostbased-pathbased.png|800]]

#### Simple Storage System. 
- <span style="color:rgb(0, 255, 0)">Note</span> Object Storage invented to deal with it with API Calls. 


