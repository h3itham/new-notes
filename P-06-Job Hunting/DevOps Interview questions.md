#### General 

1. <span style="color:rgb(255, 0, 0)">What is the difference between VM and container? </span>
	   - **Architecture** 
		   - VM have completely different Operating system. 
		   - Container: share host operating system. 
	   - **isolation** 
	     - VM is completely isolated from host. 
	     - Container less isolated 
	   - **Resources consumptions** 
	    - VM Resource intensive
	    - Container light weight 

2. <span style='color:red'>What books do you read? </span> 
   - Kubernetes in action "Marko locasa"
   - Learning DevOps "Michel krief "
   - Docker In action "Jeif Nikloff"
   - Terraform up and running "uvigin breakman"
   ![](books.png)

2. <span style='color:red'>what is the delta backup mean?</span> 
   The difference between the last backup and current changes in database. 
3. <span style='color:red'>what is the difference between sync and async in development?</span> 
   - sync "Send request and wait for response."
   - async "Fire and forget "
4. <span style='color:red'>What is the database Engine mean?</span> 
   It is a software component that responsible for managing how data is stored, retrieved and manipulated. 
5. <span style="color:rgb(255, 0, 0)">What is the best the different stages in CI pipelines? </span>
    1. Building Stage (Docker, Gradel). 
    2. Unit Tests 
    3. Static code analysis (Sonarqube, Code linting). 

6. <span style="color:rgb(255, 0, 0)">What is the artificate in any CI/CD Pipline? </span>
   Files Generated during different build stages. and you can use them in different stage. 
   
  7.<span style="color:rgb(255, 0, 0)"> What is prometheus? </span>
   - Time series database which pull metrics from different sources and store them. 
   - Monitoring system  that collect metrics from systems and applications. 
   - store them inside time series database. 

8. <span style="color:rgb(255, 0, 0)">what is the advantages of MongoDB or NoSQL database? </span>
   - Flexbility: change schema of database any time you need. 
   - Scaling: Distribute data on different shards across different nodes. 

9. <span style="color:rgb(255, 0, 0)">Why we running database on kuberetes? </span>
	- support for  micro servcie architecture. 
	- High availability
10. <span style="color:rgb(255, 0, 0)">What is Devops? </span>
    - set of tools and practices which facilitate collaboration between development teams (Dev) and OPeration (Ops)
    - Focus on delivering software product faster. 

11. <span style="color:rgb(255, 0, 0)">What is the benfits of devops? </span>
    **Business**
    - Deliver Software faster. 
    - Fullfil Gap between Development team and Operaton team. 
	  **Technical**
	  - Faster problems Resolution. 
	   ![](devopsbenfits.png)

10. <span style="color:rgb(255, 0, 0)">What are the Anti-patterns in Devops?</span> 
	Anti-patterns in DevOps refer to common practices or behaviors that are considered counterproductive (نتائج عكسية) to the principle and goals of Devops. 

	- One Person Responsible for One task (One point of Failure)
	  ![](onepersoneonetask.png)
	- Treat Production environment different from development environment such don't adding security in development environment 
	   ![](deveproduc.png)

11. <span style="color:rgb(255, 0, 0)">What is the difference between unit testing and integration testing? </span>
    - Unit Test: Break code into the smallest testable units and verify each in isolation. 
    - Integration Test: combine units and ensure they work together as intended. 

12. <span style="color:rgb(255, 0, 0)">What is multistage docker container? </span>
	- Devide stages to 
		- Build stage.(build code) 
		- Production stage. (just use the artiface) 
    - binifits 
	    - Reduce Image size. 
	    - Decrease attack surface. 
13. <span style="color:rgb(255, 0, 0)">Why you want to be Devops Engineer? </span>
    - Market needs. 
    - I invest a lot of time in learning meany technologies and i want to integrate between them. 

14. <span style="color:rgb(255, 0, 0)">What is cloud computing? </span>
     - is deliver servcie (compute, storage, network, software solutions) over the internet 
     - pay as you go
     - serve you slef. 
       ![](cloudcomputing.png)
   
15. <span style="color:rgb(255, 0, 0)">what is the different between cloud and vertualization? </span>
    - Cloud is a service or model 
    - vertualization is technology. 

16. <span style="color:rgb(255, 0, 0)">What is the different types of hypervisors? </span>
    1. Type 1 "bare-metal"
       - ESXI
       - ProxMox 
	 2. Hosted Hypervisor
	   - virtualbox 
	   - vmware 
	   - hyper-v 

17. <span style="color:rgb(255, 0, 0)">What is API? </span>
    - Application Programmable Interface 
    - Set of rules and protocols which allow communication between to different software. 
      ![](api.png)

18. <span style="color:rgb(255, 0, 0)">What is cloud Model? </span>
    1. Infrastructure as service (IaaS)
    2. Platform as a Service (PaaS)
    3. Software as a service (SaaS)
       Note that cloud responsability model from one model to another. 

19. <span style="color:rgb(255, 0, 0)">What is ceph tehcnology? </span>
	 is distributed storage system that offer the folloiwng 
	 1. Block storage. 
	 2. Object Storage. 
	 3. file storage. 
	 
20.<span style="color:rgb(255, 0, 0)"> What is vlan is one word? </span>
     it is logical separation of physical Network. 

21. <span style="color:rgb(255, 0, 0)">what is cloud init?</span> 
	- Automate the initialization of cloud instance before boot. 
	- pass shell scripts to instances. 

22. <span style="color:rgb(255, 0, 0)">what is the core operations of Devops with application Development (code) and with infrastructure?</span> 
      ![](build.png)
      Planning, Coding, Building, Backaging, Testing 

23. <span style="color:rgb(255, 0, 0)">What is the type of HTTP requests Methods? </span>
      1. GET for retrieving data from a server. 
      2. POST for submitting data to server. 
      3. PUT for updating data to server.
      4. DELETE for removing data from server. 

24. <span style="color:rgb(255, 0, 0)">Explain what you would check if a Linux build server suddenly starts getting slow?</span> 
    I will work on three axis. 
    - Operating system
	    1. CPU
	    2. RAM
	    3. Disk Space
	    4. IOPS
	- Application Level 
		- Memory leak 
		- Bugs 
	- Dependent service 
		- Antivirus 
		- firewall 
		- Network issues
25. <span style="color:rgb(255, 0, 0)">What is the build process in Devops? </span>
	is the transaltion of what programmer write (code) to what customer can use. 
	
	![](buildmean.png)

26. <span style="color:rgb(255, 0, 0)">Explain the term of Canary release?</span> 
	- <span style="color:rgb(255, 0, 0)">Dep</span><span style="color:rgb(0, 255, 0)">loy</span><span style="color:rgb(255, 255, 0)"><span style="color:rgb(0, 176, 240)">ment</span></span> strategy used when release new feature
	- Turn about 25% of your application to the new version 
	- clients be our testers. 

27. <span style="color:rgb(255, 0, 0)">Explain Blue Green Deployment? </span>
    is two <span style="color:rgb(255, 255, 0)">identical</span> environment one of them is live (production) (<span style="color:rgb(0, 0, 255)">blue</span>) , second Environment is Active but not live (<span style="color:rgb(0, 255, 0)">Green</span>) which all development next release is on it. Once verifed, the traffic is routed to the green environment using load Balancer. 
      ![](bluegreen.png)
  
28. <span style="color:rgb(255, 0, 0)">What is the post moterm meeting with reference to Devops?</span> what went wrong and steps needed to avoid failues 
    Done to discus 
    1. What went wrong. 
    2. steps needed to avoid failure. 

  29. <span style="color:rgb(255, 0, 0)">Name three important Devops KPIs.</span>
	  - Meantime failure recovery.   
	  - Deployment frequency. 
	  - Percentage of failed deployment. 
	    ![](devopskpi.png)


30. <span style="color:rgb(255, 0, 0)">what is the need for DevOps? </span>
    1. Increase deployment freuency. 
    2. Decrease failure rate of new release.  

31. <span style="color:rgb(255, 0, 0)">What is a cluster? </span>
    Group of interconnected computers or servers perform tasks as single system. 
32. <span style="color:rgb(255, 0, 0)">What is TDD mean in software? </span>
    TDD Test Driven Development which mean that tested are written before code. 
33. <span style="color:rgb(255, 0, 0)">what did you take in your consideration while chosing Devops tool? </span>
	 1. stability
	 2. community size 
	 3. learning curve 

34. <span style="color:rgb(255, 0, 0)">what is commit in git? </span>
    code snapshot in specific time. 
     ![](commitsnapshot.png)
     
35. <span style="color:rgb(255, 0, 0)">what is merge in github? </span>
    combine two branches of codes together. 

36. <span style="color:rgb(255, 0, 0)">what is caching? How it is work? why it is importing? </span>
	  - Store the most frequent asked Data in memeory (RAM).  
	  - caching increase the speed of application and system reliability. 

37. <span style="color:rgb(255, 0, 0)">What is the stateless and statefull applications? </span>
				( <span style="color:rgb(0, 176, 240)">Every thing about session</span> ) 
	- **Stateless** mean that machines don't save user sessions on it. 
	- **Statefull** mean that machines store all sessions in server storage. 
	  
38. <span style="color:rgb(255, 0, 0)">What is reliability? </span>
    is avoiding failure as you can. 
    
39. <span style="color:rgb(255, 0, 0)">what is availability? </span>
    No Downtime. 

40. <span style="color:rgb(255, 0, 0)">what is unit test mean? </span>
    Breaking down software into smallest testable parts. 

41. <span style="color:rgb(255, 0, 0)">what is lamabda functions? </span>
    using code without provisioning or managing servers. 

42. <span style="color:rgb(255, 0, 0)">How Dose container communicate in kubernetes? </span>
	- If containers in the same pod it share the same resources network and storage. 
	- Pods communicate with overlay network between them.

43. <span style="color:rgb(255, 0, 0)">what is CNI in kubernetes? </span>
    Container Network Interface. 

44. <span style="color:rgb(255, 0, 0)">what is shift left mean? </span>
    Moving testing, quality checks, and security reviews **earlier** in the development lifecycle instead of waiting until the end. 
	Move stages from the Right side to lift side <span style="color:rgb(255, 0, 0)">why</span>? 
	for discovering and solving problems faster. 

 - <span style="color:rgb(255, 0, 0)"> What is GitOps?</span> 
   Simply Managing Infrastructure using Git.  
 
----
#### AWS 
1. <span style="color:rgb(255, 0, 0)">What is CIDR Blaock? </span>
	Simpl a block of IPs. 

2. <span style="color:rgb(255, 0, 0)">What is IAM user? (user = individual)</span>
	- Represent Individaul or service (non AWS) which interact with AWS services. 

3. <span style="color:rgb(255, 0, 0)">What is IAM Role? </span>
	- Container of permission Given for AWS service to interact with different AWS service. 

4. <span style="color:rgb(255, 0, 0)">what is AMI? </span>
	- Stands for Amazon Machine Image which is a template that provides the information of operating system and it's dependencies.

5. <span style="color:rgb(255, 0, 0)">How can we send a request to Amazon S3?</span> 
	using REST API service.
6. <span style="color:rgb(255, 0, 0)">What are the different types of instances? </span>
	- General Purpose. 
	- Compute Optimized. 
	- Memory Optimized. 
	- Storage Optimized 
	- Accelerated computing. 
7. <span style="color:rgb(255, 0, 0)">What is the difference between storage types in AWS? </span>
	- File storage: Hierarchical. 
	- Block Storage: Fixed size block
	- Object storage: Flat storagef or Objects and i'ts metadata. 
8. <span style="color:rgb(255, 0, 0)">what is pricing models parts in s3</span>
	- There are two pricing model for s3 
		1. Storage Price. 
		2. Retrieval price. 
9. <span style="color:rgb(255, 0, 0)">What is the difference between identity base policy and resource  based policy?</span>
	First "Identity" in aws is (use, Group, Application)
	- Identity based policy: is the policy that attached to AWS identities. 
	- Resouce base policy: is the policy that attached to AWS resources (s3, EC2, ...)
10. <span style="color:rgb(255, 0, 0)">How can we prevent object accidental delete in s3 bucket?</span> 
	- Enable versioning on bucket. 
	- Backet policy 
	- Use MFA
11. <span style="color:rgb(255, 0, 0)">What is STS?</span> 
	secure token service which enable you to request temporary credential limited privilege. 
12. <span style="color:rgb(255, 0, 0)">How can you send email to sysadmin if EC2 instance stoped?</span> 
	I will use two main services 
	- SNS 
		1. Create a topic 
		2. subscribttion with email. 
	- Event Bridge 
		1. Event Source 
			In this section you create a new resource which is how to trigger a message to target. 
		2. Trigger a email. 
13. <span style="color:rgb(255, 0, 0)">How can you give user from out side AWS temporary access to object in s3 bucket?</span> 
	Use pre-signed url on this object 
14. <span style="color:rgb(255, 0, 0)">What is API Gateway?</span> 
	is a full managed service that makes it easy for developers to create, publish maintain, monitor and secure APIs at any scale. 
15. <span style="color:rgb(255, 0, 0)">What is the default storage class in s3?</span> 
	standard frequently accessed. 
16. <span style="color:rgb(255, 0, 0)">What are Roles</span>
	Roles are container permissions gives to Entities (User, VMs, software) to gain access to specific resources. 
17. <span style="color:rgb(255, 0, 0)">What is the edge location used for?</span> 
	- caching area. 
	- CDN Near to Customer, to reduce latency. 
18. <span style="color:rgb(255, 0, 0)">Name the AWS service that exists only to redundantly cache data and images?</span> 
	- AWS Cloud Front. 
19. <span style="color:rgb(255, 0, 0)">Explain Geo Restriction in ClouFront?</span> 
	- Prevent geographic locations from accessing content. 
20. <span style="color:rgb(255, 0, 0)">What is the difference between Cloud Watch and Cloud Trail?</span> 
	- Cloud Watch store Runtime Metrics. 
	- Cloud Trail Store All API logs. 
21. <span style="color:rgb(255, 0, 0)">In which situation you will select provisionned IOPS over standard RDS storage?</span> 
	- Performance Requirements or intensive data application. 
22. <span style="color:rgb(255, 0, 0)">What is the different between Launch configuration and launch template?</span> 
	- Launch template is the next generation from Launch template. 
	- Launch template is recommended from AWS because 
		1. Versioning. 
		2. Allow selecting different instances types. (On demand, spot)
23. <span style="color:rgb(255, 0, 0)">Name some AWS services which are not region specific? </span>
	- IAM 
	- Route 53
	- Cloud front 
#### AWS Scenario based questions
1. <span style="color:rgb(255, 0, 0)">You have been assigned to design a vpc architecture for a 2-tier application. The application needs to be highly available and scalable how would you design the VPC architecture? </span>
	
	- Create four subnets (Private, Public) the public subnet would contain the load balancers and accessible from the internet. 
	- For high availability I will use multiple availability zones. 
	- For Scalability: I will use auto scaling groups if i use instances or I'll use ECS or EKS. 
2. <span style="color:rgb(255, 0, 0)">Your organization has a VPC with multiple subnets. you want to restrict outbound internet access for resources in one subnet, but allow outbound internet access for resources in another subnet. how would you achieve this? </span>
	- Using Route table to make subnet private I will delete the default route `0.0.0.0/0`
	- Keep the default route for the public subnet and point it to the internet gateway. 
3. <span style="color:rgb(255, 0, 0)">You have a VPC with public subnet and private subnet. instances in the private subnet need to access the internet for software updates. How would you allow internet access for instances in the private subnets? </span>
	- Add Natgateway in the public subnet. 
	- Edit the default rout in the route table to point to the Nategateway. 
4. <span style="color:rgb(255, 0, 0)">You have launched EC2 instances in your VPC, and you want them to communicate with each other using private IP addresses. what steps would you take to enable this communications?</span>
	- I think that if the two machines in the same VPC they will talk with Private IPs. 
5. <span style="color:rgb(255, 0, 0)">You want to implement strict network access control for your VPC resources. How would you achieve this</span>
	Using ACL Access Control Lists. 
6. <span style="color:rgb(255, 0, 0)">Your organization requires an isolated environment within the VPC for running sensitive workloads. How would you set up this isolated environment?</span>
	- I will but all the workload in private subnet "with no default route". 
	- If this workload need Internet access I will create a NAT gateway and put it's IP in the default route. 
7. <span style="color:rgb(255, 0, 0)">Your application needs to access AWS services, such as s3 secuely within your VPC. How would you achieve this?</span>
	- I will create an endpoint for this services. 
8. <span style="color:rgb(255, 0, 0)">Explain the steps to setup a secured VPC with subnets and everything?</span> 
	- Determine the CIDR block, Enable DNS resolutions. 
	- Create Subnets in different availability zones. 
	- Define and create NACLs and Security groups. 
	- Configure Routing tables. 
	- Enable Monitoring 


#### Linux 
1. How can you list your cpu info? 
	```bash 
	cat /proc/cpuinof
	```
2. How can you see your current kernel version 
	```bash 
	uname -a 
	```
3. What is the difference between softlink and hardlink? 
	- Soft Link
		1. Like windows shortcuts
		2. Pointer to original file
		3. If you delete it the main or original data dose not change. 
			```bash 
			ln -s original_file [shortcutName]
			```
	- Hard Link
		1. Include original file content (same data)
			```bash 
			ln orignalfile [Name]
			```
4. You need to secure ssh access on a server what will you do? 
	1. Change default port number from `etc/ssh/sshd_config`. 
	2. Disable Root login 
	3. Configure firewall to allow specific IPs only. 

5. What will you do if user report that he can't write in specific directory? 
	- First I will ensure that this user has the right permissions to write in this Dir. 
	- Change permissions with `chmod` or I can make him owner 

6. You need to schedule backup for specific folder at specific period? 
	- I'll use cron job to do that. and open it's editor file with `corontab -e ` command 

7. A critical application has crashed, and you need to gather diagnostic information? 
	- First I'll check application logs if there. 
	- Then chekc all system logs `/var/log/syslog`, `/var/log/messages`
8. what is `rsync` command? 
	File synchronization and data transfer utility. 
	```bash 
	rsync -avz /path/tosource /path/to/destination/
	```

9. What is the difference between process and threads? 
	All of them are execution unit 
	- Process: stand alone entities which have it's own memory and resouces. 
	- Threads: small units within a process sharing the same memory space and resouce. 
			![[test.gif]]

10. what is `find` command? 
	```bash 
	find /directory/ filename
	```
11. What is the `/etc/reslove.conf` file ? 
	It is the config file which is used to identify DNS Server configuration resolver. 

12. what is the difference between relative and absolute path? 
	- Relative path from your current working directory. 
	- Absolute path from the main root directoy. 

13. What is the difference between process and daemon? 
	- Process run in both frontground and background. 
	- Daemon only run into the background. 
14. What is the network ponding on linux? 
	- It is simply combine more than one interface to be one interface. 
15. What is umask command do? 
	- Used to set default permissions for file or directory. 
	- **subtracted** from the default permissions to determine the actual permissions for new files and directories.

16. Explain how you would recursively remove empty directories in specific dir? 
	```bash 
	find . -type d -delete
	```
17. What do you understand about the standard streams? 
	1. `stdin` "Standard input"
	2. `stout` "Standard output"
	3. `stderr` "Standard error"
18. How you can mount new partitions 
	```bash 
	mount /dev/Dev_name /mount/point/
	```
19. How you can unmount devices 
	```bash 
	umount /dev/dev_name
	```
#### CICD 
1. What is CI/CD pipeline? 
		![[P-06-Job Hunting/img/3.png]]
	- Continuous Integration: Software development practice for Building & Testing code Every time team member push code to source control. 
	- Continuous Deployment: automatic deploy code into staging environment without manual apploval. 
	- Continuous Delivery: deploy code into production environment with human approval. 
	
2. When should I use Github action Or Jenkins? 
	- Jenkins 
		1. Self Hosted Solutions. 
		2. Customization. 
		3. Scalability (More than one server)
		4. Extensive Plugin support. 
	- Github Actions 
		1. Managed Service. 
		2. Simple and small projects. 
		3. Cost Effective.

3. What is the difference between decalartive and scripted pipeline in jenkins? 
	- Declartive is most moder one. 

4. How do you schedule Jenkins job? 
	- using cron job from build trigger on job configuration. 

5. What are strategies would you used to scalke jenkins? 
	- Jenkins architecture is scalable by nature (Master, Worker)
	- Work it on Docker or Kubernetes. 

6. By Using Multi-branch pipeline and write Jenkins file for each Environment. 

7. What Scripting language is jenkins pipeline syntax based on? 
	Groovey. 

8. What is the different ways to trigger pipeline 
	1. webhook
	2. pull SCM 
	3. Cron Job 
	4. Manual
9. What is the different between parameter and environment variables in jenkins? 
	- Parameters 
		values that can passed to job when it is triggered. 
	- Environment variables 
		system variables which is available for every job running on jenkins server. 

10. What is the different ways to take backup for jenkins server? 
	1. Take snapshot from the entire file system. 
	2. Using Jenkins plugins "thinBakup"

11. If there is a broken build in your Jenkins project, then what will you do? 
	- I will check console output. 

12. 
	
#### Docker
1. what is the main problem that docker solve? 
   Docker solve Classic problems which "software work on my mahine and don't work on another mahine. "

--- 
#### Kubernetes 
- <span style="color:rgb(255, 0, 0)">What is kubernetes? </span>
  Container Orchestration tool. 
  
- <span style="color:rgb(255, 0, 0)">What is the purpose of Haproxy-ingress and Nginx-ingress? </span>
  Act as ingress controller which route traffic to specific backend service. 

- <span style="color:rgb(255, 0, 0)">What is the exit code which indicates that there is an application error? </span>
  simple is it `1`
  
- <span style="color:rgb(255, 0, 0)">What is exit code indicates the application tried to access non-existent file?</span> 
  simple is it `127` Which indicate that file is not found. 
  
- <span style="color:rgb(255, 0, 0)">What is static pod in kubernetes?</span> [ ACME- SAICO interview]
  Pod managed with kublet, Not API server. 
  
- <span style="color:rgb(255, 0, 0)">What is the different between kubernetes controller and operator?</span> 
	- **Controller** is component of master node which achieve the desire state of the cluster. 
	- **Operator** Extend the kubernetes API to create custom resources to serve application. 

- <span style="color:rgb(255, 0, 0)">What is orchestration when it comes to software and DevOps?</span> 
  Orchestration refers to the integration of multiple servcies. 
  
- <span style="color:rgb(255, 0, 0)">What is the difference between docker and kubernetes? </span>
  - Docker is containerization tool which create, Delete, and update containers. 
  - Kubernetes is orchestration tool which manage, scale and deploy containerized application. 
  
- <span style="color:rgb(255, 0, 0)">Why we use pod rather than container in kubernetes? </span>
 <center> <b> Isolation principle in container</b> </center> 
    Container Designed to handle one process, but some application consist from several process so we need high level structure which contain more than one container (POD)

- <span style="color:rgb(255, 0, 0)">Can you create pod with two containers and how?</span> 
  yes you can create two container in the same time. 
  ```yaml
    apiVersion: v1
	kind: Pod
	metadata:
	  name: pod-with-two-containers
	spec:
	  containers:
	  - name: container1
	    image: image1
	  - name: container2
	    image: image2
    ```

- <span style="color:rgb(255, 0, 0)">Why you choose kubernetes for orchestration?</span> 
	- speed deployment. 
	- self Healing. 
	- Auto scaling 
	![](P-06-Job%20Hunting/img/kubernetesbefints.png)

- <span style="color:rgb(255, 0, 0)">What is the master node architecture?</span> 
  - API server. 
  - Kube-scheduler (assign pods to nodes)
  - Etcd Database (store cluster state)
  - controller manager (state of pods)
  - Cloud controller manager. 

- <span style="color:rgb(255, 0, 0)">What is the worker node architecture?</span> 
	1. Kube proxy for managing networking between nodes. 
	2. Kubelet is kubernetes agent. 
	3. Docker runtime. 

- <span style="color:rgb(255, 0, 0)">What is Persistent volume and Persistent volume claim used for?</span> 
	1. The main purpose is <span style="color:rgb(0, 176, 240)">isolate or decouple</span> the storage from the pod life cycle.  
	2. Abstract of storage. 
	3. Persistent volume claim is simple "Request" to use persisten volume storage. 
	
- <span style="color:rgb(255, 0, 0)">What is the k8s Operator?</span> 
	- Custom resource which Extend kubernetes API server with custom resources. 
	- Run infinite loop to achieve the desire state of system.
	
- <span style="color:rgb(255, 0, 0)">How dose kubernetes scheduler determine where to place pods? </span>
   By checking the following. 
	- Resource availability. 
	- Node capacity constrains 
	- Pod Afinity. 
	- Node taint & toleration.

- <span style="color:rgb(255, 0, 0)">Explain how kubernetes handle secret management. what are some best practices for using secrets? </span>
	- Store them as base64 value. 
	- Store them at etcd. 

- <span style="color:rgb(255, 0, 0)">How would you implement zero downtime Deployment in kubernetes? </span>
	1. Deployment 
	   By using the suitable Deployment strategy [ Rolling update, Blue/Green deployment, and canary ]
	   Take care bout health check in the application. 

	2. Service mesh 
	   For more controlled traffic routing and fault injection. 
	
- <span style="color:rgb(255, 0, 0)">Discuss the strategies for managing stateful Application in kubernetes? </span>
	  1. Use Persistent volume (PV) and Persistent Volume Claim (PVC) for storing Data. 
	  2. Backup/Restore Strategies. 
	  3. Use operator to manage them. 

- <span style="color:rgb(255, 0, 0)">How would you optimize Resource Usage in k8s?</span> 
	  1. Define Resource Request, and Limit. 
	  2. Define Horizontal Pod Autoscalers (HPA). 
	  3. Use Monitoring tool like promethesus. 

- <span style="color:rgb(255, 0, 0)">Describe How can you secure k8s cluster?</span>
  I will work on three axes. 
	  1. Linux Node it self
	     - Applying regulars updates. 
	     - Restrict assess. 
	       
	2.  K8s cluster.
	    - Restrict API server access control. 
	    - Implementing RBAC Role Based Access Control. 
	    - Use Service mesh to control the traffic between resources. 
	
	1.  Pods OR Application 
		- Image Security. 
		- Pod Security standard
		- Secret Managment. 

- How can you ensure high availability for etc in k8s culster? 
	- Deploy Multi Master nodes to ensure that etcd will be available all the time. 
	- Regular backup etcd. 
	<center> <span style="color:rgb(0, 255, 0)">Network Latency and IOPS speed has significant impact on etcd work. </span> </center> 

- <span style="color:rgb(255, 0, 0)">Discuss the role of service mesh?</span>
	- Service mesh is infrastructure layer. 
	- Play important role in service communication and control traffic flow. 
	- Used In micro-service architecture. 

- <span style="color:rgb(255, 0, 0)">How would you approach the kubernetes capacity planning? </span>
	- First I will depend on the existing metrics and logs. 
	- I will try to predict future through project market plan. 
	- Implement Observability on the cluster.
	- Implementing auto-scaling strategies for both nodes and pods. 

- <span style="color:rgb(255, 0, 0)">What is the haedless service?</span> 
	- First Let's  start with the following introduction. 
		- Each Service type in kubernetes such as clusterIP, Loadbalancer, or Nodeport. Forwards traffic to one radom pod.
		- So we can say that all types of kubernetes service is a stand alone entity that act as proxy or loadbalancer. 
		- What will you do if you want to access a specific pod? 
		- Take In consieration that when you perfrom a DNS lookup for aservcie. the DNS returns a single IP which is The service IP.
		- But if you tell kubernetes you don't need a cluster IP for your service. The DNS will return the pod IPs instead of the single service IP.
	
	- Dose not act as a proxy or Loadbalancer for accessing pods. instead, it allow direct access to pods. 

- <span style="color:rgb(255, 0, 0)">Steps to upgrade kubernetes cluster? </span>
	- First refer Official Documentation (specific release). 
	- Drain control plan 
	- Upgrade the new version (kubelet, kubectl)
	- Rejoin the control plan to cluster. 
	- Drain Worker plan. 
	- upgrade the new version (Kubelete, kubectl). 

#### Terraform
1. <span style="color:rgb(255, 0, 0)">What is terraform? </span>
	- Infrastructure as a code. 
	- use simple declarative language. 

2. <span style="color:rgb(255, 0, 0)"> What problem that terrafrom solve? </span>
	- Configuration Drift - infrastructure Drift 
	   (Every Server has  different configuration)
	- Versioning and collaboration between teams. 

3. <span style="color:rgb(255, 0, 0)">What is `init` command do ? </span>
	- Initialize working Directory. 
	- Download Provider plugins 
	- Intialize Backend. 

4. <span style="color:rgb(255, 0, 0)">How to create multiple instance in terraform? </span>
	- Use "Count" Argument
	- Example 
	```terraform
	    resouce "aws_instance" "Testserver"{
	     ami = ""
	     instance_type = "t2.micro"
	     count =3 
	     subnet_id = ""
	    }
	 ```

5. <span style="color:rgb(255, 0, 0)">How to create multiple instance with unique name?</span> 
	- use `count.index` attribute which represents the unique index number 
	  ```terraform 
	   resource "aws_instance" "TestServer" 
            ami = " " 
            instance_type = "t2.micro" 
            count = 3 
            subnet_id = ""
            tags = { 
                Name = 'TestServer-$(count.index+1)'
                }
		```


6. <span style="color:rgb(255, 0, 0)">Why we can't use ansible to create infrastructure such as terraform?</span> 
	- State file. (main reason)
	- Terraform is declarative 

7. <span style="color:rgb(255, 0, 0)">In your Openion what is the strenght point of terraform?</span> 
	- Idempotent. 
	- Use Declarative language.
	- work with different Providers. 
	- Collaboration and versioning. 

8. <span style="color:rgb(255, 0, 0)">what is `terraform validate` command do? </span>
   - validate all terraform syntax. 


9. <span style="color:rgb(255, 0, 0)">What will you do if you want to delete specific resource in terraform?</span>
	- I will use `terraform destroy [target]` with target parameter. 


10. <span style="color:rgb(255, 0, 0)">What is the different modules in terraform? </span>
	- Root Module current working directory which contain `provider.tf` file 
	- child Module 
	- Public module. 

11. <span style="color:rgb(255, 0, 0)">How can we manage terraform code in different environment? </span>
	- Using terraform workspaces

12. <span style="color:rgb(255, 0, 0)">what is the drawbacks of terraform? </span>
    1. Managing state file. 
    2. Learning curve it take a lot of time. 
       
13. <span style="color:rgb(255, 0, 0)">What is local variables? </span>
	- Named value that computed within modules. 
	- Scope "only in current module"
	- You can't set them from an input. 

14. <span style="color:rgb(255, 0, 0)">waht is the lifecycle resource in terraform?</span> 
	- Nested block inside terraform resource which decide the life cycle 
	- Examples (Create before destory, prevent destroy.)

15. <span style="color:rgb(255, 0, 0)">How we can provide terraform with information I mean input? </span>
	- Variables. 
	- Data Source. 


16. <span style="color:rgb(255, 0, 0)">what is Null resource in terraform? </span>
    - Null Resource not create any resources but you can use it to create provisioners. 
    - what is provisioners? 
      Used to execute command or script. 

17. <span style="color:rgb(255, 0, 0)">what do you mean with terraform cloud? </span>
    - Enables teams to use Terraform together. 

18. <span style="color:rgb(255, 0, 0)">List Some terraform CLI? </span>
    1. `terraform init`
    2. `terrafrom plan`
    3. `terraform validate`
    4. `terraform apply`
    5. `terraform refresh` 

19. <span style="color:rgb(255, 0, 0)">How to ignore</span> <span style="color:rgb(255, 255, 0)">duplicate</span> <span style="color:rgb(255, 0, 0)">resource error during terraform apply</span>? 
	- **Why this error happen (First question).** 
	    1. You ( or someone else ) have executed you terraform code and you don't have a shared / updated state. 
	     2. Resources created manually 
	     3. Terrafrom destroy faild in a way that deleted the resources 
	- **Solutions** 
		1. Delete resources from terraform code to stop managing them with it. 
		2. Delete resources from console. 
		3. Import this to terraform code. 

20. <span style="color:rgb(255, 0, 0)">How to store sensitive Data in Terraform?</span>
	1. Integrate with secret management systems such as aws secret manager and<span style="color:rgb(255, 255, 0)"> hashicorp vult</span>. 
	2. You can use Environment Variable. 

21. <span style="color:rgb(255, 0, 0)">Explain State File locking?</span> 
    - Lock sate file when two developers work on the same time to avoid confilicts. 
22. <span style="color:rgb(255, 0, 0)">How will you control and handle rollbacks when something goes worng?</span>
    1. Revert to the previous commit in github. 
    2. apply this file with terraform.


23. <span style="color:rgb(255, 0, 0)">How dose terraform ensure the indempotency of resource</span> provisioning? 
    By Maintaing a state file. 

24. <span style="color:rgb(255, 0, 0)">Descibe the lifecycle of a terraform resources?</span>
	1. Create Resource
	2. Update Resouce
	3. Delete Resource. 

25. <span style="color:rgb(255, 0, 0)">Explain the concept of terraform workspaces and when to use them?</span> 
    workspace allow us to create different configuration for different environment. 
    
 26.<span style="color:rgb(255, 0, 0)"> What is state file and what is it's importance</span>? 
     - state file "json file" which <span style="color:rgb(0, 176, 240)">track</span> all infrastructure created with terraform. 
	 - Track resources
	 -  Allow collaboration between teams. 
	   
26. <span style="color:rgb(255, 0, 0)">what is terraform backend?</span> 
    it is location which store state file may be 
    - Local storage 
    - Remote 
	      - s3, Google Cloud stroage GCS. 
	        
27. <span style="color:rgb(255, 0, 0)">What is Module in terrafrom?</span> 
    Group of related resources. Which we can reuse them in different projects. 

28. What is the important of terrafgrun for terraform? 
	- It just a wrapper (auxiliary tool) which used to add new functionalities to terraform such as 
	   managing remote state. 

29. <span style="color:rgb(255, 0, 0)">Explain State locking in terraform? </span>
	- Locking state file to avoid conflicts in two user try to apply terraform code in the same time. 

30. <span style="color:rgb(255, 0, 0)">How to lock terraform module version?</span> 
    - Hard coded it in the module 
      ```yaml
      module "example" {
      source = "terraform-module-name"
      version = "x.y.z" # Specify the version you want to lock
      } 
		```

31. How would you upgrade plugin in terraform. 
    use `terraform init ` with `--upgrade`

32. What is terraform file structure? 
    ![](terraform-structure.png)


#### CICD

#### Jenkins 

- <span style="color:rgb(255, 0, 0)">What are Jenkinsfile, Pipeline and multibranch?</span> 
	- Jenkinsfile: file which written in Groovy syntax
	- Pipline: Jenkins Job that execute automated stateges such as build, test and deploy. 
	- Multibranch: Piepline which create jobs based on git branches. 
	
-  <span style="color:rgb(255, 0, 0)">what are different types of Jenkins Jobs? </span>
	   1. FreeStyle Jobs 
	   2. Pipeline Job use <span style="color:rgb(0, 176, 240)">jenkinsfile</span> 
	   3. Multi branch Job
	  
-  <span style="color:rgb(255, 0, 0)">Explain different between Declarative and scripted</span> <span style="color:rgb(255, 0, 0)">pipeline in jenkins? </span> 
	 - Syntax 
		 Declarative --> Yaml file 
		 Scripted     --> Grovey
	- Readability 
		  Declarative --> Easier to read and maintain. 
		  Scripted     --> Harder 
	- Use case 
		  Declarative --> Standard CI/CD 
		  Scripted      --> Advanced workflow. 

- <span style="color:rgb(255, 0, 0)">How do you trigger a jenkins pipeline automatically?</span> 
	1. Webhock 
	2. Pull SCM 
	3. Trigger from different job. 
	4. scheduled builds 
	5. API call
	
- <span style="color:rgb(255, 0, 0)">What is the purpose of post section in a jenkins piplein?</span>
  Run after pipeline stage completes. You can use it to send notification after the pipeline is complete. 

- <span style="color:rgb(255, 0, 0)">How do you integrate Jenkins with Docker/Kubernetes? </span>
  There are plugins for both technologies. 

- What is Jenkins shared libraries? 
  Reusable set of Groovy scripts that help modularize pipeline code. 

- How can you implement Rollback strategies in jenkins? 
	- You can use the previous Artifact "Image". 
	- You can revert the last commit in git. 


#### Gitlab CI/CD
- Jobs in gitlab CI/CD is self contained this mean that you can't share resource with each other. 
  "<span style="color:rgb(0, 176, 240)">Artifact</span>" can do
- Stage is group of jobs. 
- Jobs in same stages can run in parallel. 

#### System Design interview 
1. <span style="color:rgb(255, 0, 0)">magine you're given a list of 30 million HTTP endpoints. Your task is to design a script or system that classifies each endpoint as either 'working' or 'not working'. How would you approach this problem in an efficient, scalable, and fault-tolerant way?
</span>
	High level appraoch 
	1. Concurrency: (Multithreading, async connection)
	2. Timeout: to avoid slow Endpoint 
	3. Retries: retry check the endpoint in differnt interval.  