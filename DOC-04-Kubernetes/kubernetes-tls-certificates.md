#### Termenologies 
- TLS Certificates: Used to secure communication between client and server by enabling encryption. 
- CA: Trusted Entity that issues digital certificates. 
- CSRs: Encoded file that an entity (e.g., a website owner or organization) generates and sends to a Certificate Authority (CA) to request a digital certificate
#### Introuction 
- SSL/TLS Handshake Process Diagram
	![](https.png)

- TCP Three way hand check 
	![](threewayhandcheck.png)

#### Kubernetes 
- There are two types of certificates authorities (public, private)
- Kubernetes Private CA create certs for all component. 
- Kubernetes Certificates folder `/etc/kubernetes/pki` 
	![[kubernetes-certs.png]]
- check certificates expirations 
```bash 
kubeadm certs check-expiration
```
- How dose worker nodes get a certificate
	There are different methods depend on cluster setup 
	- Manual Certificate Management
		- Certificates can be manually placed on worker nodes at `/var/lib/kubelet/pki/`
		- The kubelet uses these certificates to authenticate with the API server
	- Certificate Signing Requests (CSR)
		- Kubelet can generate a private key and Certificate Signing Request
		- Sends CSR to the Kubernetes API server
		- A cluster administrator or controller signs and approves the CSR
		- Kubelet retrieves the signed certificate

- How dose pods communicate with API-server? 
	- Every pod get Service Account (SA = special type of accounts which used by pods to authenticate to API ). 
	- Kubernetes Automatically mount Certificate, Token at 
		```bash 
		/var/run/secrets/kubernetes.io/serviceaccount/
	   ```

![[kubernetes-communication.png]]
