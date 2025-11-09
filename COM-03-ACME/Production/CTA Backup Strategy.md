#### Kubernetes Cluster Level Backup
- We'll use `velero` to take backup from our k8s cluster. 
- We'll use `MinIO` to store backup. 
- We'll backup the entire k8s cluster. 
- The backup will be will be every 2 days. 
- Retention policy will be about 30 Days. 
#### MinIO 
- There is a dedicated machine in the production which have `MinIO` Installed on it. IP `10.10.0.40`
#### Velero 
