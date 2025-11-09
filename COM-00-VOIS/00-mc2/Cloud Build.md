- There are two pipelines 
	- Dags from github to composer bucket. 
	- Data Pipelines form github to Datafusion 
-  Cloud Build configuration file 
	```txt
	vf-grp-mc2-cloudbuild-nonlive.yml, vf-grp-mc2-cloudbuild-live.yml 
	```
- Pipelien Code explanation 
	- Github Project 
		```txt 
		https://github.vodafone.com/VFGroup-NextGenBI/MC2-cloud-build
		```
	- Cloud Build alpha 
		```txt
		cloud_composer/cloudbuild/check_composer_alpha.yaml
		```
	- Subsititution stage in pipeline Show files (Added, Deleted, Modified). The general purpose from this step is to define the new dags files. 
- Production vault link 
	```txt 
	https://gvp.vault.neuron.bdp.vodafone.com/ui/vault/auth?redirect_to=%2Fvault%2Fsecrets&with=VFGroup-CloudAnalytics%2F
	```
- To access it you need to create access token from github itself. 
- `check_dag` walk through 
	1. Add vault to hosts 
	2. Retry function mechanism to retry command to avoid
		1. Network Problems 
		2. Vault unavailability 
	3. Colone Repository 
	4. Get Different between the current and previous Commit 
	5. Validate Dag files (Python or yml)
##### Squid 
- HTTP Forwarding Proxy 
- Restrict Access 
- 