## Manual form console 
- Architectural
	![[task-architecture.png]]
- Create a repository for image 
- Enable Identity aware proxy API. 
- Configure Oauth Screen (one perproject)
	- APIS & service 
	- OAuth consent screen 
- Create SA and named it (haitham-task-sa) without any roles
#### Cloud Run 
- Select Image from Registry 
- Region (europe-west1)
- Just select the service account you created from security tab. 
- You can use (IAM) to grant user access on it, Select the service and in the top right corner you will found permission tab 
- Note that browsers by default dose not send token to now that your service is good 
	- Fetch your token 
		```bash 
		 TOKEN=$(gcloud auth print-identity-token)
		```
	- Curl Request 
		```bash 
		curl -H "Authorization: Bearer $TOKEN" [CloudRun-URL]
		```
- Change `ingress Control` to be `interanl` from **Networking** tab 
		![[loadbalancer-03.png]]
	This lead that you can't access it any more form browser 
		![[loadbaalncer04.png]]
- Grant IAP access on Cloud run with default service account of IAP 
	```bash 
	service-[projectNum]@gcp-sa-iap.iam.gserviceaccount.com
	```
#### Cloud LoadBalancer 
- Deploy Application loadbalancer 
- First create certficate from moutaz script. 
- Frontend configuation 
	- Add certificate 
	- Enable HTTP to HTTPs redirect 
- Backend Configuration 
	- Create new backend service 
		- type `serverless network endpoint group`
	- Disable CDN, Allow Logs 
#### IAP 
- trun on IAP on the loadbalancer you created. 
- Add your email as princible on IAP  