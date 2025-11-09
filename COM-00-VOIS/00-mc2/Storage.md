- Check statefile first (Every Changes). 
- Naming convention of all storage projects `vf-(lm)-mc2-(env).yml` Example `vf-ie-mc2-nonlive.yml`∆
- There is no user have access, it is group access. (Generic IAM modules). 
- When we need to use the same module in the same project we need to change it's name or use `resource_name_override` <span style="color:rgb(255, 0, 0)">why</span>? to avoid resource name conflicts.
- <span style="color:rgb(255, 0, 0)">What is the difference between consumer and enterprise (Storage Standard) ?</span> 
	Consumer: perform analytics and generate insights from Customer Data. 
	Enterprise: perform analttics and generqte insights from vodafone data. 
		![[enterprise-consumer.png]]
- Types 
	1. Storage Projects
		Every local Market have storage project, suitable to store data for less than 2 months. 
	2. Datahub 
		Store Projects <span style="color:rgb(0, 255, 0)">Live</span> Data.  
- Vodafone information classification defined based on it's sensitivity and potential impact. 
	1. `c1` public ifno (Published marketing material, press releases, job postings.) 
	2. `c2` Vodafone internal  (Internal org charts, meeting minutes, intranet articles, anonymized data.)
	3. `c3` Vodafone confidential (Customer personal data, internal financials, strategic plans)
	4. `c4` Vodafone Secrets (M&A plans, unreleased financial results, critical security credentials.)
- What is the difference between logical storage and physical storage? 
	different concepts related to how data is stored and billed. 
		**Logical**: The amount of un **compressed** data visible to user (columns)
		**Physical**: the actual **compressed** data stored in disk.  
#### Code Walk through 
#### Module mc2_buckets_tf12
- Create all required buckets for the tenant only if tenant is not `grp`
- You need to review [[Terraform#Local]]
- Local doing some advanced logic and iteration to generate useful values. 
- How conditional work on terraform 
	```hcl 
	condition ? value_if_true : value_if_false
	```
	
- Examples 
	- Local_markets 
		```hcl
		local_markets = var.tenant == "grp" || var.tenant == "mc2dev" ? var.additional_local_markets : [var.tenant]
		```
		- If the tenant is `"grp"` or `"mc2dev"`, then use the list in `var.additional_local_markets`.
		- Otherwise, just make a single-item list from the tenant name.
		    ➡️ **Purpose:** Dynamically define the list of local markets based on tenant type.

	-  nonlive_only and live_only
		```hcl 
		nonlive_only = var.stage == "live" ? [] : local.local_markets
		live_only = var.stage == "live" ? local.local_markets : []
		```
		- `nonlive_only` includes markets **only if stage is not `"live"`**.
		- `live_only` includes markets **only if stage is `"live"`**.
- There is two types of buckets and Datasets 
	- Standard (Created from terraform module)
	- Custom based on local market needs 
- `flatten` is used to convert list of lists into a single flat list. 
#### Module mc2_bigquery_datasets_tf12 
- Automate Bigquery dataset provisioning and permissions. 
- Create dataset and provide permission or access 
	- Service Account for different services 
	- Group of users such as data teams.

#### MC2 Generic IAM. 
- We don't grant access to user but we do with groups 
- In neuron there are many teams such as (Data teams, analytics team, ETL teams)