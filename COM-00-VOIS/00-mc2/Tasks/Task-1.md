#### Provide read write access NEDS 
- Project: vf-ie-mc2-nonlive
- SA: vf-ie-neds-orb-adv-dp-sa@vf-grp-neds-nonlive-trusted.iam.gserviceaccount.com 
- bucket: vf-mc2et-ie-nonlive-test-data
- I grant all permissions to service acount 
- <span style="color:rgb(255, 0, 0)">Note</span> all datasets has access to vf-ie-mc2et-dev-dp-ds-sa@vf-grp-mc2compute-nonlive.iam.gserviceaccount.com
##### Steps 
1. check if bucket exist in statefile  (exist)
#### General Notes 
- NEDS is a data migration project which move data from source to MC2. 
- Every bucket need `objectViewer` Permission to change ot write accessj
- Every changes in terraform try to add it to the last lines of configuration to avoid confilicts. 

Statefile bucketname should be at `grp/vf-grp-mirep-datahub/terraform.tfstate`


#### Important notes 
- After you talk approval from the project maintainer which you can get from the right of the page 
- You need to create new tag or release new tag which you will use with GoCD. 