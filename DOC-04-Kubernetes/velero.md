#### Install MinIO (backup storage)
1. Installation using docker container 
```bash
docker run -itd \
  -p 9000:9000 \
  -p 9001:9001 \
  --name minio \
  -v ~/minio/data:/data \
  -e MINIO_ROOT_USER=haitham \
  -e MINIO_ROOT_PASSWORD=Elabd@1998 \
  quay.io/minio/minio server /data --console-address ":9001"
```
2. Manually create bucket in the minio dashboard name it `velero`

#### Install velero 
1. Download the package 
	```bash 
	wget https://github.com/vmware-tanzu/velero/releases/download/v1.14.1/velero-v1.14.1-linux-amd64.tar.gz
	```
2. Extract file 
	```bash 
	tar xvfz velero-v1.14.1-linux-amd64.tar.gz
	```
3. Move velero to bin folder 
	```bash 
	 mv velero-v1.14.1-linux-amd64/velero /usr/bin
	```
4. Create Credential command 
	```bahs 
	# vim minio-credential
	[default]
	aws_access_key_id = haitham
	aws_secret_access_key = Elabd@1998
	```
5. Create velero 
	🗒 <span style='color:red'>BackutName & Minio URL</span> 
	```bash
	velero install \
	--provider aws \
	--plugins velero/velero-plugin-for-aws:v1.2.1 \
	--bucket velero \
	--secret-file ./minio-credential \
	--use-volume-snapshots=false \
	--use-node-agent \
	--uploader-type=restic \
	--backup-location-config region=minio,s3ForcePathStyle=true,s3Url=http://192.168.2.50:9000
	```


6. To remove or uninstall `velero` use the following command 
	```bsh
	velero uninstall
	```
#### Backup Scenario 
1. Create a namepace 
	```bash 
	kubectl create namespace test-velero
	```
2. Create a Nginx Deployment 
	```bash 
	kubectl create deployment nginx --image=nginx -n test-velero
	```

3. Create a backup 
	```bash
	velero backup create firstbackup --include-namespaces test-velero 
	```

4. After backup creation you delete the name space and try to restore it
	```bash
	kubectl delete namespace test-velero
	```
5. Restore backup 
	```bash
	velero restore create firstbackup-restore --from-backup firstbackup
	```
6. To delete the backup 
	```bash
	kubectl delete backup firstbackup -n velero
	```


#### If you changed the Minio storage URL 
1. Delete the old one 
	```bash
	kubectl delete backupstoragelocation default -n velero
	```
2. Reinstall velero 
	```bash
	velero install \
	--provider aws \
	--plugins velero/velero-plugin-for-aws:v1.2.1 \
	--bucket velero \
	--secret-file ./minio-credential \
	--use-volume-snapshots=false \
	--backup-location-config region=minio,s3ForcePathStyle="true",s3Url=http://10.0.0.100:9000
	```
