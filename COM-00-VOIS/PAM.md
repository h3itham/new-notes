- What is the purpose of using PAM? 
	- Just In Time access (JIT). 
	- Enhance Security 
	- Policy as a code (All policies in one Repo)
	- Auditing and compliance. 
- Used to Give (User, SA) Temporary access to resource
- There is a repo which contain projects policies in neuron. So they use `github` provider in the code. 
	- Policy as a code 
	- Automation: Terraform can Fetch the latest polices from github repo. 
	- Centralization: All project polices in one place. 
- Entitlement: Set of permissions that can be granted to users. 
	- Entitlement Components 
		1. Which Roles. 
		2. who can Request access to these roles (Eligible User). 
		3. Who can Approve (Approvers). 
		4. How log (Max duration)
		5. Who gets notified ()