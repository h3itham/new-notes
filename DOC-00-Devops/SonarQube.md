#### Installation 
- On kubernetes 
	```bash 
	helm repo add sonarqube https://SonarSource.github.io/helm-chart-sonarqube
	helm repo update
	helm show values sonarqube/sonarqube > sonarqube-values.yaml
    helm install sonarqube sonarqube/sonarqube -n sonar --create-namespace \
	  --set community.enabled=true \
	  --set monitoringPasscode="haitham"
	```
#### Integration 
##### Github 
You can refer the following documentation [sonarqube](https://www.coachdevops.com/2024/02/how-to-integrate-sonarqube-with-github.html)
