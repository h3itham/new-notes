#### Introduction 
- Makes Kubernetes resources Discoverable via public DNS servers. 
- Allow you to control DNS records dynamically via kubernetes resources In DNS provider agnostic way.
#### Create EKS Cluster for testing 
  
- with `eksctl` tool 
	```bash
	eksctl create cluster \
	  --name hivebox-cluster \
	  --version 1.29 \
	  --region us-east-1 \
	  --nodegroup-name linux-nodes \
	  --node-type t3.medium \
	  --nodes 2
	```
#### Steps 
1. Create `IAM` Policy with the following permissions 🗒 <span style='color:red'>Copy policy ARN</span> 
	```bash 
		aws iam create-policy \
		  --policy-name externaldns-policy \
		  --policy-document '{
		    "Version": "2012-10-17",
		    "Statement": [
		        {
		            "Effect": "Allow",
		            "Action": "route53:ChangeResourceRecordSets",
		            "Resource": "arn:aws:route53:::hostedzone/*"
		        },
		        {
		            "Effect": "Allow",
		            "Action": [
		                "route53:ListHostedZones",
		                "route53:ListResourceRecordSets",
		                "route53:ListTagsForResource"
		            ],
		            "Resource": "*"
		        }
		    ]
		}'
	```

1. Use the policy to create an IAM role for the service account:
	```bash
	eksctl utils associate-iam-oidc-provider --region=us-east-1 --cluster=hivebox-cluster --approve 
	```
	
	```bash 
	eksctl create iamserviceaccount \
	  --cluster=hivebox-cluster \
	  --name=externaldns \
	  --namespace=default \
	  --attach-policy-arn=arn:aws:iam::047719625140:policy/externaldns-policy \
	  --override-existing-serviceaccounts \
	  --approve
	```

1. To check that all is good 
	```bash 
	 kubectl get sa -n kube-system  | grep dns 
	```
2. Run the following command to determine if RBAC is turned on in your Amazon EKS cluster
	```bash 
	kubectl api-versions | grep rbac.authorization.k8s.io
	```
3. Apply externalDNS 
	```yaml
	apiVersion: rbac.authorization.k8s.io/v1
	kind: ClusterRole
	metadata:
	  name: externaldns
	  labels:
	    app.kubernetes.io/name: externaldns
	rules:
	  - apiGroups: [""]
	    resources: ["services", "endpoints", "pods", "nodes"]
	    verbs: ["get", "watch", "list"]
	  - apiGroups: ["extensions", "networking.k8s.io"]
	    resources: ["ingresses"]
	    verbs: ["get", "watch", "list"]
	---
	apiVersion: rbac.authorization.k8s.io/v1
	kind: ClusterRoleBinding
	metadata:
	  name: externaldns-viewer
	  labels:
	    app.kubernetes.io/name: externaldns
	roleRef:
	  apiGroup: rbac.authorization.k8s.io
	  kind: ClusterRole
	  name: externaldns
	subjects:
	  - kind: ServiceAccount
	    name: externaldns
	    namespace: default # Change to the desired namespace if needed
	---
	apiVersion: apps/v1
	kind: Deployment
	metadata:
	  name: externaldns
	  labels:
	    app.kubernetes.io/name: externaldns
	spec:
	  strategy:
	    type: Recreate
	  selector:
	    matchLabels:
	      app.kubernetes.io/name: externaldns
	  template:
	    metadata:
	      labels:
	        app.kubernetes.io/name: externaldns
	    spec:
	      serviceAccountName: externaldns
	      containers:
	        - name: externaldns
	          image: k8s.gcr.io/external-dns/external-dns:v0.14.0
	          args:
	            - --source=service
	            - --source=ingress
	            - --domain-filter=haithamelabd.com # Only process hosted zones matching this domain
	            - --provider=aws
	            - --policy=upsert-only # Prevents ExternalDNS from deleting records
	            - --aws-zone-type=public # Only look at public hosted zones
	            - --registry=txt
	            - --txt-owner-id=Z0184902SG2FKJOI60T6
	          env:
	            - name: AWS_DEFAULT_REGION
	              value: us-east-1 # Change to the region where your EKS cluster is located
	```
