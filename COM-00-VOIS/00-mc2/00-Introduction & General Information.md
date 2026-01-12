- MC2: stands for Minimum capability. 
- Focused in financial reporting. Expanded later to include telecom business.
- Designed to support **monthly data provisioning** from local markets (e.g., Germany) to Vodafone Group’s finance reporting systems. 
- Utliise existing GCP Data Lakes across vodafone such as Data Hub projects for local markets. 
- Every Local Market has three project 
	- Storage Project: For MC2 ETL Team only.  
	- Datahub Project: For All ETL from any team.  
	- Compute Project
- Datahub is different from storage project in the following 
	- It is only storage project which contain live data for All programs such as Neds, AI booster 
#### HLD 
![[HLD.png|500]]
#### VPC-SC 
In Neuron there are three environment 
- Alpha 
- beta
- zeta 
but in mc2 we use only Alpha and zeta 
![[vpc-sc.drawio.png]]
#### General 
1. DHK used to store Data Encryption Key used by datahub. 
2. CMK is Regional resource.
3. Every local market has it's own SA which have access to specific Datahub. 
4. Every local maket restricted with specific region 
	1. Every Local Market --> europ-weast-1 
	2. UK --> europe-weast-2.    
	3. DE(GR) --> europe-weast-3 
5. This `tenant_core_tf12` module is used to create all VPC and Network component need for project. 
6. Subnets for dev
	1. `DEV`
	2. `QA`
	3. `PrePord`
7. subnets for production 
	1. `proda`
	2. `prodb`
8. Test Project which we work on it `vf-mc2dev-ca-nolinve`
	1. All subnets which will be created 

9. `Orbiter` is a digital marketing tool designed for 
	1. Improve lead conversation.  
	2. Reduce cost per Acquisition through campaign and channel optimization. 
10. All Docker images in neuron stored in the following project `vf-grp-neuronenabler-liv`
#### Composer 
- Orchestrator based on airflow. 
- Python Package Index (pypi): is official repository for third party packages, Which use `pip` to install them. 
#### Storage Projects. 
- There is no user access it is group access, service account . (Generic IAM modules). 
- What is the difference between consumer and enterprise (Storage Standard) ? 
	Consumer: perform analytics and generate insights from Customer Data. 
	Enterprise: perform analttics and generqte insights from vodafone data. 
		![[enterprise-consumer.png]]
- Types 
	1. Storage Projects
		Every local Market have storage project, suitable to store data for less than 2 months. 
	2. Datahub 
		Store **Live** Data. 
- Vodafone information classification defined based on it's sensitivity and potential impact. 
	1. `c1` public ifno (Published marketing material, press releases, job postings.) 
	2. `c2` Vodafone internal  (Internal org charts, meeting minutes, intranet articles, anonymized data.)
	3. `c3` Vodafone confidential (Customer personal data, internal financials, strategic plans)
	4. `c4` Vodafone Secrets (M&A plans, unreleased financial results, critical security credentials.)
- Code Walk through 
	- Module `mc2_buckets_tf12`
		- The module will create all required buckets for the tenant only if tenant is not `grp`. 
		- You need to review [[Terraform#Local]]
		- Local doing some advanced logic and iteration to generate useful values 
