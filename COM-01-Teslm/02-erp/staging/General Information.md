#### Important Paths 
1. **Sites**: `/home/frappe/frappe-bench/sites`
	- site configuratiosn `site_config.json`
	- Uploaded files 
2. **Logs**: `/home/frappe/frappe-bench/logs`
	- Application Logs 
	- Worker Logs 
- Cloud Map 
	- Enable Service Connect to allow ECS tasks to discover and connect with each other. 
	- Backbone of microservice architecture. 
- Task execution role: 
	- is a special IAM role that grants permissions o the Amzaon ECS agent (EC2, Farget) to set up and manage tasks. 
#### Communication between services 
- Communication between services done with **broker** <span style="color:rgb(255, 0, 0)">Redis</span> 