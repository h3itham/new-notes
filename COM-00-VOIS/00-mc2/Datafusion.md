- Basics 
	[[Google Data Engineering Course#Datafusion ! datafusion.png 30]]
- Part of compute projects, `vf-grp-mc2compute-live` `vf-grp-mc2compute-nonlive`. 
- Namespaces and Compute profiles created with python script, this script use subnets name to create namespace nonlive (dev, preprod, qa) live (proda, prodb)
- Create Profile and base base_profiel
	- script location 
		```txt 
		neuron-gcp-platform/modules/datafusion_tf12/main/scripts/multitenant_mc2_script/create_datafusion_profile.sh
		```
	- Script command 
		```bash 
		sh -x platform/modules/datafusion_tf12/main/scripts/multitenant_mc2_script/create_datafusion_profile.sh vf-mc2dev-ca-nonlive europe-west1 datafusion-dev-enterprise  vpc-mc2dev-non-live-vpc datafusion-sa@vf-mc2dlive.iam.gserviceaccount.com modules/datafusion_tf12/main ie,al,es mc2cp,mc2et
		```
	- script input explanation 
		```txt 
		- PROJECT_ID     vf-mc2dev-ca-nonlive 
		- REGION          europe-west1 
		- INSTANCENAME    datafusion-dev-enterprise 
		- VPCNAME.        vpc-mc2dev-non-live-vpc
		- CDF_SA      
		- JSON_TEMP_PATH  modules/datafusion_tf12/main
		- LM              ie,al,es
		- TENANt          mc2cp,mc2et (consumer, enterprise)
		```
- Namespaces for data isolations 
- RBAC Enable it from module it sefl,  RBAC is permission on All Instances and All namespaces. After Enable RBAC you should found the following 
  ![[rbac-datafusion.png]]
  You can add permission from there but if you can do this from command live (in the same documentation)
- All configurations found at `SYSTEM ADMIN`
	![[system-admin-cdf.png]]
- Scripts use subnets name 
- Grant user, group, SA access on specific namespace 
	- Documention  [Link](https://docs.cloud.google.com/data-fusion/docs/how-to/create-role-based-access-control#rbac-grant-predefined-roles)
- Script to Grant Access to one principle for all namespaces 
	```bash
	#!/bin/bash  
	  
	PROJECT="vf-mc2dev-ca-nonlive"  
	INSTANCE_ID=datafusion-dev-enterprise  
	REGION="europe-west1"  
	USER_ID="mostafa.yehiamorsy@vodafone.com"  
	USER_TYPE="user"  
	ROLE="roles/datafusion.editor"  
	  
	# Get the CDAP endpoint  
	CDAP_ENDPOINT=$(gcloud beta data-fusion instances describe \  
	  --location="$REGION" \  
	  --format="value(apiEndpoint)" \  
	  "$INSTANCE_ID" \  
	  --project="$PROJECT")  
	  
	# Get OAuth token for authentication  
	AUTH_TOKEN=$(gcloud auth print-access-token)  
	  
	# Fetch namespaces from CDAP API  
	NAMESPACES=$(curl -s -H "Authorization: Bearer $AUTH_TOKEN" \  
	  -H "Content-Type: application/json" \  
	  "$CDAP_ENDPOINT/v3/namespaces" | jq -r '.[].name')  
	  
	# Loop over each namespace and grant the role  
	for NAMESPACE in $NAMESPACES; do  
	  echo "[INFO] Granting $ROLE to $USER_ID in namespace $NAMESPACE..."  
	  gcloud beta data-fusion add-iam-policy-binding "$INSTANCE_ID" \  
	    --project "$PROJECT" \  
	    --location "$REGION" \  
	    --namespace "$NAMESPACE" \  
	    --member="${USER_TYPE}:${USER_ID}" \  
	    --role="$ROLE"  
	done  
	  
	echo "[INFO] Done granting roles in all namespaces."
	```

