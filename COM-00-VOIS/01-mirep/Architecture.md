#### Introduction 
- The system takes raw data files (from sources like SharePoint), ingests them into GCP, and then runs a series of independet transformation jobs in BigQuery to create useful, aggregated data.
- Transformation process has interdependencies, meaning not all process can run concurrently. 
-  The data is all in the perimeter defined to C4, and the data at rest is saved encrypted.
- There are Four process types 
	1. Dimension 
	2. Semantic 
	3. Staging 
	4. Backend file. 
#### Dimension Process. 
- Architecture 
	![[dimension-arch.png]]
- Initiated upon new file arrived `bucketName`  bucket form `ProjectName` Project 
- There is a topic `mi-dimension` which in `vf-grp-mirep-datahub-beta` and subscription is the cloudrun 

#### Semantic 
- Architecture
	![[semantic-arch.png]]
- This process start with `scheduler` which run ever 5 minute to trigger a cloudrun which have specific condition. 

#### To Remember 
- Modules names 
```txt 
pubsub                     pubsubtopics 
subscription               pubsub_subscription_tf12
```