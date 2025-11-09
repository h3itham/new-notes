- Project naming convention `vf-(lm)-datahub.yml`
- You should first create `dhk` for this project 
- Module name `datahub_v2_tf12`, python-based templater that reads yaml file and convert them to terraform code (mainly storage buckets and bigQuery Datasets)
- This module have three sections 
	1. Service List 
	2. Service Account List 
	3. Storage Type (buckets, datasets) with it's permission 
- All buckets created for Datahub have names starting with `vf(lm)-dh` so when you have task with it search with end name. 
- there is a very important folder which is `main/service_configuration` which is a common folder which contain all service which you can inherit from. Don't forget to add filename under `service_list`. this file naming convention `mc2_(serviceName)` this file contain local mark storage 
- For every datahub there is a key which used to encrypt the content of the datahub whether is it is bucket or dataset. which name is `vf-ro-dhk.yml`

#### General 
- Project for every LM. 
- Name convention `vf.(LM).datahub.yaml`
- Every DataHub in different LM has specific use cases, So you can have multiple buckets and data sets. 
- Dataset name should start with `lake` such as `lake_subscriberinfo_rawprepared_s` but when you run terraform apply the module it self will change the name to be `vfro_dflake_subscriberinfo_rawprepared_s`
- Service Account (SA): Special Type of account. 