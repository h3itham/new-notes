Hello everyone,  
I’m Haitham, Platform Engineer from Squad 11 (MC²).

We received a ticket from the NEDS team requesting to grant access for their new service account  
`vf-de-neds-prd-tru-adv-sa@vf-de-neds-prd-tru-adv-live.iam.gserviceaccount.com`  
on the following resources:

- Dataset: `vfde_dh_lake_mc2_orbiter_rawprepared_s`
    
- Bucket: `vfde-dh-orbiter-rawingested`
    

The purpose of this change is to upgrade from **NEDS 1.0 to NEDS 2.0** as part of their migration process.  
They already have pipelines running on DE LM, and this migration only moves them from one platform to another — with **no changes in data source or destination**.

This change is already documented in the following PR:  
[neuron-gcp-project-configs/pull/21733](https://github.vodafone.com/VFGroup-CloudAnalytics/neuron-gcp-project-configs/pull/21733)

In summary, we are simply **adding the new NEDS 2.0 service account** to the dataset and bucket, and **updating references from NEDS 1.0 to NEDS 2.0**.

For any additional information or clarification, I’ll hand over to Shaimaa, our Scrum Master, who can provide more context.