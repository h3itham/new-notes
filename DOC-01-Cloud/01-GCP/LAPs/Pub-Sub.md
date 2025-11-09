#### Lap Description
- This lab do the following just create `tobic` and `bucket` and when new object uploaded to the bucket. it send notification to `topic`. 
#### Command Line 
1. Enable APIs 
	```bash 
	gcloud services enable storage.googleapis.com pubsub.googleapis.com
	```
2. Create a pub/sub topic 
	```bash 
	gcloud pubsub topics create bucket-notifications
	```
3. Create bucket 
	```bash 
	BUCKET_NAME=my-pubsub-bucket-$RANDOM
	gsutil mb -l europe-west3 gs://$BUCKET_NAME/
	```
4. Grant Service Account access 
	```bash 
	# GET ACCOUNT NUMBER 
	gcloud projects describe $PROJECT_ID --format="value(projectNumber)"
	# GRANT PERMISSIOn 
	gcloud pubsub topics add-iam-policy-binding bucket-notifications \
	  --member="serviceAccount:service-<PROJECT_NUMBER>@gs-project-accounts.iam.gserviceaccount.com" \
	  --role="roles/pubsub.publisher"
	```
5. Grant Permission to pub/sub 
	```bash 
	PROJECT_ID=$(gcloud config get-value project)
	TOPIC="projects/$PROJECT_ID/topics/bucket-notifications"
	gsutil notification create -t $TOPIC -f json gs://$BUCKET_NAME
	```
	you can verify that with 
	```bash 
	gsutil notification list gs://$BUCKET_NAME
	```
6. Test Notification 
	- Upload file 
		```bash 
		gcloud pubsub subscriptions create bucket-sub --topic=bucket-notifications
		gcloud pubsub subscriptions pull bucket-sub --auto-ack
		```
7. Delete All resources 
	```bash 
	# Variables
	PROJECT_ID=$(gcloud config get-value project)
	BUCKET_NAME=$(gsutil ls | grep "my-pubsub-bucket" | head -n 1 | cut -d'/' -f3)
	TOPIC_NAME="bucket-notifications"
	SUB_NAME="bucket-sub"
	
	# Delete notification config (no harm if none exist)
	gsutil notification delete gs://$BUCKET_NAME || true
	
	# Delete subscription
	gcloud pubsub subscriptions delete $SUB_NAME --quiet || true
	
	# Delete topic
	gcloud pubsub topics delete $TOPIC_NAME --quiet || true
	
	# Delete bucket and its contents
	gsutil -m rm -r gs://$BUCKET_NAME || true
	```