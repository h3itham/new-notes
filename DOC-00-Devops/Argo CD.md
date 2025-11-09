#### Introduction 
- Kubernetes native continuous Development tool. 
- Use git repository as a source of truth. 
##### Before Argo 
- There was different approaches to Deploy new software on servers 
	1. Manually 
	2. Automatic using different tool such as (Jenkins, ciricleCi) all this tall trigger deployment to kubernetes using `kubectl` command. But there was some challenges such as 
		- Configure access to kubernetes and install it's client`kubectl` 
		- If you are using kubernetes clusters on the cloud (AWS, GCP, and Auzre) you need to Configure access to it too. 
		- Security challenge. 
		- No visibility of deployment status. 
##### With Argo 
- Declarative and version control. 
- Automatically sync Git repository. 
- Support multticluster deployment. 		
#### Installation 
##### Kubernetes 
2. Create namespace 
	```bash 
	kubectl create namespace argocd
	```
3. Apply the following file 
	```bash 
	kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
	```
4. To retrieve the admin password 
	```bash 
	kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 -d
	```

#### Integration 
##### Github 
1. Create Access token with the following permissions <span style="color:rgb(0, 176, 240)">Developer settings</span> 
	- Repo scope Full access. 
	- Admin:rep_hook full access 
	- User section 
	  read:user for Read All user profile data. 
2. Create kubernetes secrete for sync this repo 
	```yaml 
	apiVersion: v1
	kind: Secret
	metadata: 
	  name: argo-private-repo-secret
	  namespace: argocd
	  labels:
	    argocd.argoproj.io/secret-type: repository # important label
	stringData:
	  type: git
	  url: https://github.com/h3itham/argo.git
	  username: h3itham 
	  password: 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 # Token which you created in the previous step.
	```