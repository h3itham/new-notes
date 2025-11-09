This deamo for create kubernetes user with specific permissions. The following will be a set up for RBAC in kubernetes to manage user permissions per namespace. 

- Generate user certificate In specific folder 
	1. Create certificate folder 
		```bash 
		mkdir crt && cd crt 
		```
	2. Generate private key 
		```bash
		openssl genrsa -out developer.key 204
		```
	3. Generate Certificate signing request 
		```bash 
		openssl req -new -key developer.key -out developer.csr -subj "/CN=developer/O=development"
		```
	4. Generate base64 encoded for both files 
		```bash
		cat developer.key | base64 -w 0 > key.base64 
		cat developer.csr | base64 -w 0 > cert.base64
		```

- Kubernetes manifests 
	I will Create testing namespace for this Demo 
	1. Namesapce 
		```yaml
		# 00-namespace.yaml
		apiVersion: v1
		kind: Namespace
		metadata:
		  name: team-namespace
		```

	2. Create a Role that defines permissions
		```yaml 
		# 01-roles.yaml
		apiVersion: rbac.authorization.k8s.io/v1
		kind: Role
		metadata:
		  namespace: team-namespace
		  name: developer-role
		rules:
		- apiGroups: [""]  # "" indicates the core API group
		  resources: ["pods", "services", "configmaps"]
		  verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]
		- apiGroups: ["apps"]
		  resources: ["deployments"]
		  verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]
		
		```

	3. Create a RoleBinding to assign the Role to a user
		```yaml
		# 02-role-binding.yaml
		apiVersion: rbac.authorization.k8s.io/v1
		kind: RoleBinding
		metadata:
		  name: developer-binding
		  namespace: team-namespace
		subjects:
		- kind: User
		  name: developer  # Replace with actual user
		  apiGroup: rbac.authorization.k8s.io
		roleRef:
		  kind: Role
		  name: developer-role
		  apiGroup: rbac.authorization.k8s.io
		
		```
	4. Certificate Signing Request (CSR) for the user
		```yaml
		# 03-certicate-signing-request.yaml
		apiVersion: certificates.k8s.io/v1
		kind: CertificateSigningRequest
		metadata:
		  name: developer-csr
		spec:
		  request: <base64-encoded-csr> # Replace with actual CSR
		  signerName: kubernetes.io/kube-apiserver-client
		  expirationSeconds: 31536000  # 1 year
		  usages:
		    - client auth
		```
	5. Approve developer certificate 
		```bash 
		kubectl certificate approve developer-csr 
		```
	6. Generate approved certificate 
		```bash
		kubectl get csr developer-csr -o jsonpath='{.status.certificate}' | base64 -d > crt/developer.crt
		cat developer.crt | base64 -w 0 > crt.base64 
		```
	7.  Create kube config template 
		```yaml
		apiVersion: v1
		kind: Config
		clusters:
		- cluster:
		    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURCVENDQWUyZ0F3SUJBZ0lJZXZPTmpBcUVwZlF3RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TkRBM01USXlNak0zTkRCYUZ3MHpOREEzTVRBeU1qUXlOREJhTUJVeApFekFSQmdOVkJBTVRDbXQxWW1WeWJtVjBaWE13Z2dFaU1BMEdDU3FHU0liM0RRRUJBUVVBQTRJQkR3QXdnZ0VLCkFvSUJBUURaTmdibVN6TGk2MzZrcGRCSDlXWWQ5TllGalhWUERDblNOY0VOcjJ1UlBCOFVHUUgzYWttRm9ZRFIKcEVibTBsQXdIYmFqQTBNb0Z5dFdBKzhvTVlGdjU1L1NxT0E0VjI1K0tkMDF6MG5icGsrR2ZJSXpZL0NqT29TUQpTQ0JUYi95b21EQlhyandlR0NiWXVKdG5FWW16L0dSZjEzK0d1SHhIVjNUdUdqTGgweUYvbi9GaDlSZm9KU0lsCjhERnhYR0V2Y2dyd0VNQmhnV3dPWDN2MjE4V2ZFeFdqUWxYWXhLMFVkRWkwWTJMaEpINnNzTUhxKzV3K0g2cjMKV1hTV2JRSTcxOTFxcUlMZVFwcE1aK3poT3E4SzB3anBSVTF1bFhDWXpuWXVheUxvbzdaa1dZQUNsTDhpVExnbAo4TC91TlpXV3NlM0F6STBVM3c0RkVzYzExNXloQWdNQkFBR2pXVEJYTUE0R0ExVWREd0VCL3dRRUF3SUNwREFQCkJnTlZIUk1CQWY4RUJUQURBUUgvTUIwR0ExVWREZ1FXQkJTZ2xiY3JYY0Vqald6RHVGb3daUkdidVFpSUREQVYKQmdOVkhSRUVEakFNZ2dwcmRXSmxjbTVsZEdWek1BMEdDU3FHU0liM0RRRUJDd1VBQTRJQkFRQTFJVUZzNXgzcwp3dG0vVE40WVFxWC8zckc5ZW14QXk4b2psdC9pYWFySTlHSlZ1RjY4eEpjM1JTYTR6WDhzeS9ncW5LNmxmTXhYCjdaNnBWMnNheWdhVlNsUUVvVWQ0eHpHU2Z2dDZoaEQzUWx6cEVUN1FGTTJtTEs5cHZja1BTYU93US9CdTNoWncKUXhqd2dzM2w0Zk5Fekx0MTFaQlFITXVHRTJ2ajgvaG1yZ0ZGb3U3Si9HaVFqV0NGSklXVFlONjNIUHdaSU5TMwpjS3JOdTZheDJ4cVpLcUlIZnpVNm83MkltcE5yNVB3U2xQRk85b1pISm9kKzcwRnRoRm1nc2U5L0s1VWlpZU9KClFDa01ZNHFpbzJqYWJlZXpCUi93MGFZaVhPT1N5Y1dJNW94aDdtUml4UmMxbWcwbU5ZdnpuTzByRHdlS3lWTzYKSEV4bW9sSVlaeU1rCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
		    server: https://10.0.0.101:6443   # Replace with cluster endpoint
		  name: kubernetes
		contexts:
		- context:
		    cluster: kubernetes
		    user: developer
		    namespace: team-namespace
		  name: developer-context
		current-context: developer-context
		users:
		- name: developer
		  user:
		    client-certificate-data:  LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURFRENDQWZpZ0F3SUJBZ0lSQU1wZUpPNVdKMXFjbWpaRnRsTUR5YlF3RFFZSktvWklodmNOQVFFTEJRQXcKRlRFVE1CRUdBMVVFQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TlRBeU1EY3dPVFEwTXpSYUZ3MHlOakF5TURjdwpPVFEwTXpSYU1Db3hGREFTQmdOVkJBb1RDMlJsZG1Wc2IzQnRaVzUwTVJJd0VBWURWUVFERXdsa1pYWmxiRzl3ClpYSXdnZ0VpTUEwR0NTcUdTSWIzRFFFQkFRVUFBNElCRHdBd2dnRUtBb0lCQVFDeDV3a1V3bFlqcDJ3OUpBVisKTFo5L0J3WllNM2hMS2NnTE55MGdTdmUwRllocngxbFFiWTZ6S2d0Y0pGbzRDTU9xeEpuYVpUVmpYNUdZVXRhSQpRdEkrMFhiL0ZpdVR1RTZKREp3Q041SlRVNk5TNjczZXlrV2podVpKL2ZGY3NjQUU0aFI1SklxY1IxV2RvT0Z3CmwrYzkxL0dpS3NyVmlEc04zMFpEMkZUaHZ0VjZBQk1QeFVibHVGRjNreHlrdnRXS1NGWU5NSjE3ZThiaTVBS3YKMk04MnYwTG5VWmtnVFJtNzBKbUN2MnEwaUVjR2RFNE41MmVLVUpxWkxIL0VhSmpQOWFhcmNBV2FMdVBtZ3lEMQpaanhINmgwcSthMm5YWUxWaTE3QkE0aHFGNUhYTTdjQkdKUDZWeTNHdk5ESGlmTnRuR05nRG9jVUt0WkdmVGNPClg2eTNBZ01CQUFHalJqQkVNQk1HQTFVZEpRUU1NQW9HQ0NzR0FRVUZCd01DTUF3R0ExVWRFd0VCL3dRQ01BQXcKSHdZRFZSMGpCQmd3Rm9BVW9KVzNLMTNCSTQxc3c3aGFNR1VSbTdrSWlBd3dEUVlKS29aSWh2Y05BUUVMQlFBRApnZ0VCQUo4OEhteVQ3Ni82T0dFaFAxb1pxRjNFYWtFU2k4MnlPS2VNVUVwLzlnTFM4OHNHWngwVUszU3BYNFFvCms0dmhBM0o5aFMwRzZLbWZTU1FHY3YvSWV6T29UNERuYnVQYUoxcFZreE1XOHpwOU00YVcvMWlpYTcvLy94SWsKYVhCUStqRzEvbXBteTJZaWFEcjhVb0Z1UVRNcXpDaWxPQUFkQThFbTdhVGlqNEhmU1NuZEdPK2gvRHdreG9zTgowNmplQTJwazVESGlwRkhTMXlqSFJiRi9BQUdzSS84RFlIVFFLeXZ4OWZFK1RVVlRjdy9obUNxTUo2WktZWndzCnFKeHJhaTlzZUdFYXIyVFhTdFNBRTNRaVBsQ24yZGw0SHlFSjJPWmhZdXpvQ2JEQW8vZHZ4Qm1jOWJuRGVhcXoKQThyZ3hVRGVqMGhtNUloQ3JFWkp3cTNPMkhrPQotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==
		    client-key-data: LS0tLS1CRUdJTiBQUklWQVRFIEtFWS0tLS0tCk1JSUV2UUlCQURBTkJna3Foa2lHOXcwQkFRRUZBQVNDQktjd2dnU2pBZ0VBQW9JQkFRQ3g1d2tVd2xZanAydzkKSkFWK0xaOS9Cd1pZTTNoTEtjZ0xOeTBnU3ZlMEZZaHJ4MWxRYlk2ektndGNKRm80Q01PcXhKbmFaVFZqWDVHWQpVdGFJUXRJKzBYYi9GaXVUdUU2SkRKd0NONUpUVTZOUzY3M2V5a1dqaHVaSi9mRmNzY0FFNGhSNUpJcWNSMVdkCm9PRndsK2M5MS9HaUtzclZpRHNOMzBaRDJGVGh2dFY2QUJNUHhVYmx1RkYza3h5a3Z0V0tTRllOTUoxN2U4YmkKNUFLdjJNODJ2MExuVVprZ1RSbTcwSm1DdjJxMGlFY0dkRTRONTJlS1VKcVpMSC9FYUpqUDlhYXJjQVdhTHVQbQpneUQxWmp4SDZoMHErYTJuWFlMVmkxN0JBNGhxRjVIWE03Y0JHSlA2VnkzR3ZOREhpZk50bkdOZ0RvY1VLdFpHCmZUY09YNnkzQWdNQkFBRUNnZ0VBUnZJYllYOTBxYWhSVm5xZ2hDYStsZ0pmcWRud3pZeStmWmFGOVB4Z2x4MEYKTi9JYWNLUVlSZVBzL3RSeWRDa3VtdEhuaHp4WVU2WmZvaTdhNWlFN3hoaHppNXUwU2ljMGpwd01OU2dqUHY2NgpRYzNJZk9vT1puM2NKY0xUTjhPZEQzVjNjNjQ2WnNOZDVjd3NTMklRc2syRlM4WWorSTdvK2duQjlEbDM5RVFpCnBnd1NXRy9GRWZVeStmdThGL0dWKzRFZkVhNHByTXRucjg4RkVPOHlDMjhlU2dwL1E3cUhUZUdWa3dzaCtiS04KV0dZa2EwRFIvaUg1RTlyZ3Y2enlXOGI1RHhwcDdnMnFvRlZpTUhtYXR6bFhvN0orcXlqc1lCT2RQdkQwa0p3OQpPamM3cGxCcWFTM2FwVkl4ajQzem5KZXdkSFU1UjNuY1JRL2xZYW1OOVFLQmdRRHRKV0RvK0VjTzQyOGN3NW9KClFvV0hFNTVhMjBWTlNyejF1bnpmYXlNeC9vaDA3S1lxNjJMUmlLc0ZwTDZYRDVDVGRyZDh1azZmeUlscldubnMKbkZMdWVwbkRMMzhTK3dKdVRON0I2RThkQTVwQ2VtcGRnbUFMR3ROekZESjhoZlBzaXJZemF5ODdQNUlRcU50SwpoTkw4alRDZWVsbytKK0hmbGE3Y1lCeG9yUUtCZ1FEQUMrQlFEcm1zemJWRllkNUpES0NueDRIREJhSWNaakpOCkw2NzdML0QxUHpkS3kvRzJXRzhxd2ErdkkvS3p0c29kVkZyWUNzaFVCem5IeENXQ2RKVWVjOFQ5NEZZZS9uSGcKSXZxcWZCRGxQZ2hlODk3WFFiVmRsaE0yZGN6bkx2RUZjcS9MS2JITkhuWkR6UC9iTFVYcG1wVjA1akozemlpbQo3Z3NmcFNFamN3S0JnUURFY1FJbCtnUml3T041NEUzby9jRytjdkhxTVZqbS9kSXBlNkdWTVJxbWYwSnFHTjVOCkQwOThZc25Ja3YrT0FzYXdZTmI5NnhiV0pUZ3dSWEphSGVSVXlCbzNKVkVPVi85YmZ6SEhUcmk2UUJHNG44OGMKUnJOb2Y0QkpBNklPcEtlNmVlMFVDdVhGSHBQUVdRL09XdGlyZkFtYVBhQWpmTVlPZjkrcGpiUDZ3UUtCZ0Y4NgpSRnRPUWZHVyswalJNN2tEUXZLUE1oL3pQaVJZNUo1emc0L1lnT0lMRzY3Q0hEUXYrOGUyelJmS2VvRjBwUm9RCkJ3L1RvT3hSdk92NnJpTFhEazVRVjZFUHlZU0s1UXFnTGEyMTRXYmE4S21zd1pYNVppWm0wVllTNmF6RkVReUQKaHZuMXpjMkpTbGxxWUlCZEhhcXRWV2ptOEtXSm00elN3cjlKVVhwVEFvR0FKc1p4SGxqM3U1VGowYjBOYW0xOAphWDNrRnhFU3N4TGVPWHAvMUxLczlaTmJwNFg3MmVKbTVFSTVJNmdwdzlTZnR2NzVsd3JreGg5ZjRmaHhtQTAvClBpU0ZLRng5Vm5nVWJibEJqK1A4bSsxaFNFZ2xTcGRDL1V4dTFBYVYzRFJickxEUGplVmdYcFNrZFJmT2RmeHMKVEtXNWp6MWlkdVBCcGFqOGFURVpON1k9Ci0tLS0tRU5EIFBSSVZBVEUgS0VZLS0tLS0K
		
		```