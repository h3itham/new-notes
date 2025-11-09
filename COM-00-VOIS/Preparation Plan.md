 - What is Neuron? 
	Vodafone next-generation cloud-based analytics program. Designed to replace legacy BigData platform. 
- What is apache NiFi? 
	Desinged to automate the flow of data between different software
 - PE Code & Components  
	 - There are two repos for project `platform`, `configuration`
	 - Regarding terraform code we don't apply the code directly we use script to force some rules before apply 
	 - Decoupling infrastructure modules from environment specific configuration. 
	 - why we use yaml file 
		 - It very easy to read and write infrastructure with it [xebia](https://xebia.com/blog/terraform-with-yaml-part-1/)
 - From `Neuron Discovery`
	- History of neuron 
	- Session 2 `IAP` "Identity Aware proxy"
		- Central Authentication layer 
		- Such as network firewall but for https & applications. 
		- You can define access policies centrally and apply them to all of your applications and resources
- Datahub 
	- From `Nash channel`
	- We have two resources of Data `buckets` and `BigQuery Datasets`
	- The Following Table define the difference permission for all used services 
		![[datahubPermission.png]]
- Neuron templating  
	- Use Ready templates to reduce time and force convention in team. 
	- use python scripts (jinja templates) to generate terraform files. 
- Neuron shared services 
#### Storage 
- There are two standard in `MC2` Enterprise & consumer, We use between them based on Performance and cost. 
