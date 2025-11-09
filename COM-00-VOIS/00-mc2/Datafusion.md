- Basics 
	[[Google Data Engineering Course#Datafusion ! datafusion.png 30]]
- Part of compute projects, `vf-grp-mc2compute-live` `vf-grp-mc2compute-nonlive`. 
- Namespaces and Compute profiles created with python script, this script use subnets name to create namespace nonlive (dev, preprod, qa) live (proda, prodb)
- Create Profile and base base_profiel
	```bash 
	neuron-gcp-platform/modules/datafusion_tf12/main/scripts/multitenant_mc2_script/create_datafusion_profile.sh
	```
- Namespaces for data isolations 
- RBAC Created from console 
- Scripts use subnets name 
- Datafusion module ``
- To start creating resouce switch to project name 
	```bash 
	switch_project.sh -p vf-mc2dev-ca-nonlive 
	```
- After that there is a `target` folder which will be created in `project-config`which contain all generated terraform code 
