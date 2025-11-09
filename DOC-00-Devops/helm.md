#### Introduction 
- Package manager for kubernetes. 
- Simplifies kubernetes application management using charts. 
- Release Management 
- Configuration management. 
#### Installation 
1. Installation on mac 
	```bash 
	brew install helm
	```
2. Verify helm installation 
	```bash 
	helm version 
	```
#### Helm Repository 
1. use repository to distribute charts, the default helm repository is [https://charts.helm.sh/stable](https://charts.helm.sh/stable.)
2. Add stable repository 
	```bash
	helm repo add stable https://charts.helm.sh/stable  
	helm repo update
	helm repo list
	```

3. Install specific helm chart
	```bash 
	helm install <installationName> <repoName>/chartName
	helm install my-mysql stable/mysql
	```
4. Customize helm chart using values file 
	- Get Default values file 
		```bash 
		helm show values stable/mysql > my-values.yaml
		```
	- Edit the value file and install it
		```bash 
		helm install my-mysql -f my-values.yaml stable/mysql
		```
	- Uninstall release 
		```bash
		helm uninstall my-mysql
		```
	