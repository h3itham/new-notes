- PR standard 
	```txt 
	action
	
	Assure upcoming PR are submitted with following comment structure
	
	1- **Scope** ( short description what we are changing and ref project jira tickets if relevant . it will enable audit activites and link to assets released )
	
	2- **Why** ( what is driving the need of change, an issue , functional evolution, data issue ..... ) 
	
	3- **Rollback** ( what steps / procedures are required to rollback if the change does not work )
	```
- Datasets 
	```txt
	We will create 3 different set of datasets:
	
	- 2 for ZETA MIREP-DEV
	    - UAT: (cloud run on vf-grp-mirep-dev)
	        - vf-grp-datahub.vfgrp_dh_lake_edw_mipyramid_uat_s
	        - vf-grp-datahub.vfgrp_dh_lake_edw_mipyramid_staging_uat_s
	    - DEV: (cloud run on vf-grp-mirep-dev)
	        - vf-grp-datahub.vfgrp_dh_lake_edw_mipyramid_live_s
	        - vf-grp-datahub.vfgrp_dh_lake_edw_mipyramid_tactical_live_s
	- 1 for ZETA MIREP-NL
	    - NL:  (cloud run on vf-grp-mirep-nonlive)
	        - vf-grp-datahub.vfgrp_dh_lake_edw_mipyramid_NL_s
	        - vf-grp-datahub.vfgrp_dh_lake_edw_mipyramid_staging_NL_s
	```