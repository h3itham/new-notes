#### Kops 
##### Introduction 
`kops` or Kubernetes operations command line which create, manage and upgrade kubernetes clusters
#### Installation 
1. Run the following commands 
	```bash 
	curl -LO https://github.com/kubernetes/kops/releases/download/$(curl -s https://api.github.com/repos/kubernetes/kops/releases/latest | grep tag_name | cut -d '"' -f 4)/kops-linux-amd64
	
	chmod +x kops-linux-amd64
	
	sudo mv kops-linux-amd64 /usr/local/bin/kops
	```


#### Setup Kubernetes Clusters 
##### AWS 
1. Create `s3 bucket` for storing `kops` objects. 
	```bash 
	aws s3api create-bucket --bucket h3itham-kops-storage --region us-east-1
	```
2. Create Kubernetes Clusters 
	```bash 
	kops create cluster --name=h3itham.k8s.local --state=s3://h3itham-kops-storage --zones=us-east-1a --node-count=1 --node-size=t3.small --master-size=t3.small  --master-volume-size=10 --node-volume-size=8
	```
3. Review and apply the cluster 
	```bash 
	kops update cluster h3itham.k8s.local --yes --state=s3://h3itham-kops-storage
	```
4. Validate the cluster 
	```bash 
	kops validate cluster --state=s3://h3itham-kops-storage
	```
5. How to connect to it 
	```bash 
	kops export kubecfg --name h3itham.k8s.local --state=s3://h3itham-kops-storage --admin 
	```

6. Delete Cluster 
	```bahs 
	kops delete cluster --name h3itham.k8s.local --state=s3://h3itham-kops-storage --yes
	```