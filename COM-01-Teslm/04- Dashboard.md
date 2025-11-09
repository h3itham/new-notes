
#### Introduction 
Dashboard is the application which serve endusers 
#### Deployment 
Dashboard is Deployed in S3 bucket 
Steps : 
1. Create an IAM user with `AmazonS3FullAccess` Policy (Download CSV). 
2. Create bucket ( enable ACLs, un tick Block all public access). 
3. Enable static file hosting. 
Github Action work follow (commit to repo)