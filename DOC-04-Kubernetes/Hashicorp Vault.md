#### Introduction 
- Tool for securely storing, accessing and managing secretes. 
- Secrets may be (passwords, API keys, Certificates)
- Provide encryption, access control and auditing to protect sensitive data. 
- Keys features.  
	1.  *Secrets Management:* Store and control access to secrets.
	2.  *Encryption as a Service:* Encrypt/decrypt data without storing it.
	3.  *Dynamic Secrets:* Generate short-lived credentials for databases, cloud providers, etc.
	4.  *Audit Logging:* Track all access to secrets.
- Secret engine: backend that handle secrets. 
- Authentication Method: User / machine prove it's identity 
- Policies: ACLs that user/machine use to authenticate 
- What dose "seal" mean in vault? 
	- Vault always stores data encrypted at reset.
	- The encryption key used for that data is encrypted itself with the master key. 
	- When vault is sealed, the master key is not loaded into memory so vault cannot decrypt any data. 
#### Installation in kubernetes (using Helm - Dev Mode)
- Add hashicorp repository 
	```bash
	helm repo add hashicorp https://helm.releases.hashicorp.com
	```
- Use the following installation if you want to enable ui and access it from outside the cluster. 
	```bash
	helm install vault hashicorp/vault \
	  --namespace vault \
	  --create-namespace \
	  --set server.dev.enabled=true \
	  --set ui.enabled=true \
	  --set ui.serviceType=LoadBalancer
	```
	
	<span style="color:rgb(255, 0, 0)">Note</span> Running Vault in “dev” mode. This requires no further setup, no state management, and no initialization. This is useful for experimenting with Vault without needing to unseal, store keys. 

	<span style="color:rgb(255, 0, 0)">Login</span> to vault UI in `DEV` mode with tooke just type `root`
#### Secrets injection methods. 
1. Vault Agent sidecar. 
	- The Vault Agent runs as a sidecar container in your pod
	- It handles authentication with Vault and secret retrieval
2. Using Vault CSI Provider. 
3. Using external secret operators. 
##### Test vault 
1. Create Service Account in the <span style="color:rgb(0, 255, 0)">namespace</span> do you want. 
	Note that SA: is identity which pod use to communicate with API server. 
	```yaml
	apiVersion: v1
	kind: ServiceAccount
	metadata:
	  name: vault-auth
	  namespace: vault
	```
2. Apply serviceAccount 
	```bash
	kubectl apply -f vault-service-account.yaml
	```
3. Access vault Pod 
	```bash
	kubectl exec -it vault-0 -n vault -- /bin/sh
	```
4. Configure Authentication 
```bash
export VAULT_TOKEN=root

# Create Read Policy 
cat <<EOF > /tmp/read-policy.hcl
path "secret/*" {
  capabilities = ["read", "list"]
}
EOF

# Apply the policy 
vault policy write read-policy /tmp/read-policy.hcl

```
Enable the K8S auth method 
```
vault auth enable kubernetes
```
Configure Vault to authenticate Kubernetes service accounts
```bash
vault write auth/kubernetes/config \
  token_reviewer_jwt="$(cat /var/run/secrets/kubernetes.io/serviceaccount/token)" \
  kubernetes_host="https://kubernetes.default.svc:443" \
  kubernetes_ca_cert=@/var/run/secrets/kubernetes.io/serviceaccount/ca.crt
```

Create a role for our service account
```bash
vault write auth/kubernetes/role/demo \
bound_service_account_names=vault-auth \
bound_service_account_namespaces=vault \
policies=read-policy \
ttl=1h
```
Enable the KV version 2 secrets engine
```bash
vault secrets enable -path=secret kv-v2
```
Create a test secret
```bash 
vault kv put secret/credentials username="dbuser" password="dbpassword"
```

5. Create Test Deployment  (Using SideCar Method)
	```yaml
	apiVersion: apps/v1
	kind: Deployment
	metadata:
	  name: vault-demo
	  namespace: vault
	  labels:
	    app: vault-demo
	spec:
	  selector:
	    matchLabels:
	      app: vault-demo
	  replicas: 1
	  template:
	    metadata:
	      labels:
	        app: vault-demo
	      annotations:
	        vault.hashicorp.com/agent-inject: "true"
	        vault.hashicorp.com/role: "demo"
	        vault.hashicorp.com/agent-inject-secret-credentials: "secret/data/credentials"
	        vault.hashicorp.com/agent-inject-template-credentials: |
	          {{- with secret "secret/data/credentials" -}}
	          username: {{ .Data.data.username }}
	          password: {{ .Data.data.password }}
	          {{- end -}}
	    spec:
	      serviceAccountName: vault-auth
	      containers:
	      - name: app
	        image: nginx:1.21
	        ports:
	        - containerPort: 80
	```

6. Apply deployment 
	```bash
	kubectl apply -f vault-test-pod.yaml
	```

7. Verify secret injection 
	```bash
	kubectl wait --for=condition=Ready -l app=vault-demo pod -n vault --timeout=300s
	# Get the name of the running pod 
	POD_NAME=$(kubectl get pod -l app=vault-demo -n vault -o jsonpath="{.items[0].metadata.name}")
	# Check if the secrets were properly injected
	kubectl exec -it $POD_NAME -n vault -- cat /vault/secrets/credentials
	```


#### Production Mode 
##### SetUp the Environment 
1. Create namespace 
	```bash 
	kubectl create namespace vault-prod
	```

2. Add Helm repository 
	```bash 
	helm repo add hashicorp https://helm.releases.hashicorp.com
	helm repo update
	helm search repo hashicorp/vault
	```
##### Prepare TLS certificate 
1. Generate self signed certificate with Moutaz script 
	```bash 
	./cert.sh vault.devopsmaffia.net vault/
	```
2. Create Kubernetes certificate 
	```bash
	# CA certificate
	kubectl -n vault-prod create secret tls tls-ca \
  --cert=./vault-cert/rootCA.crt \
  --key=./vault-cert/rootCA.key

	# Server certificate and key
	kubectl -n vault create secret tls tls-server \
  --cert=./vault-cert/vault.devopsmaffia.net.crt \
  --key=./vault-cert/vault.devopsmaffia.net.key
	```

##### Create Production values file 
```yaml 
# Vault Helm Chart Production Values Configuration
global:
  enabled: true
  tlsDisable: false

injector:
  enabled: true
  replicas: 3
  resources:
    requests:
      memory: 256Mi
      cpu: 250m
    limits:
      memory: 512Mi
      cpu: 500m

server:
  # Use specific version for production
  image:
    repository: "hashicorp/vault-enterprise"  # or "hashicorp/vault" for OSS
    tag: "1.16.1-ent"  # Pin to specific version

  # Production resource requirements based on Vault Reference Architecture
  resources:
    requests:
      memory: 1Gi
      cpu: 1000m
    limits:
      memory: 2Gi
      cpu: 1000m

  # Custom health check probes for production
  readinessProbe:
    enabled: true
    path: "/v1/sys/health?standbyok=true&sealedcode=204&uninitcode=204"
    initialDelaySeconds: 10
    periodSeconds: 10
    timeoutSeconds: 3
    successThreshold: 1
    failureThreshold: 2

  livenessProbe:
    enabled: true
    path: "/v1/sys/health?standbyok=true"
    initialDelaySeconds: 60
    periodSeconds: 5
    timeoutSeconds: 3
    successThreshold: 1
    failureThreshold: 2

  # Mount certificates and credentials
  extraVolumes:
    - type: secret
      name: tls-server
    - type: secret
      name: tls-ca
    - type: secret
      name: kms-creds  # if using GCP KMS

  # Persistent storage configuration
  dataStorage:
    enabled: true
    size: 5Gi
    storageClass: "gp2"  # or your preferred storage class
    accessMode: ReadWriteOnce

  auditStorage:
    enabled: true
    size: 10Gi
    storageClass: "gp2"
    accessMode: ReadWriteOnce

  standalone:
    enabled: false

  # High Availability configuration with 5 nodes
  ha:
    enabled: true
    replicas: 5
    raft:
      enabled: true
      setNodeId: true
      
      config: |
        ui = true
        cluster_name = "vault-production-cluster"
        
        listener "tcp" {
          address         = "[::]:8200"
          cluster_address = "[::]:8201"
          tls_cert_file   = "/vault/userconfig/tls-server/tls.crt"
          tls_key_file    = "/vault/userconfig/tls-server/tls.key"
          tls_client_ca_file = "/vault/userconfig/tls-ca/ca.crt"
        }

        storage "raft" {
          path = "/vault/data"
          
          retry_join {
            leader_api_addr = "https://vault-0.vault-internal:8200"
            leader_ca_cert_file = "/vault/userconfig/tls-ca/ca.crt"
            leader_client_cert_file = "/vault/userconfig/tls-server/tls.crt"
            leader_client_key_file = "/vault/userconfig/tls-server/tls.key"
          }
          retry_join {
            leader_api_addr = "https://vault-1.vault-internal:8200"
            leader_ca_cert_file = "/vault/userconfig/tls-ca/ca.crt"
            leader_client_cert_file = "/vault/userconfig/tls-server/tls.crt"
            leader_client_key_file = "/vault/userconfig/tls-server/tls.key"
          }
          retry_join {
            leader_api_addr = "https://vault-2.vault-internal:8200"
            leader_ca_cert_file = "/vault/userconfig/tls-ca/ca.crt"
            leader_client_cert_file = "/vault/userconfig/tls-server/tls.crt"
            leader_client_key_file = "/vault/userconfig/tls-server/tls.key"
          }
          retry_join {
            leader_api_addr = "https://vault-3.vault-internal:8200"
            leader_ca_cert_file = "/vault/userconfig/tls-ca/ca.crt"
            leader_client_cert_file = "/vault/userconfig/tls-server/tls.crt"
            leader_client_key_file = "/vault/userconfig/tls-server/tls.key"
          }
          retry_join {
            leader_api_addr = "https://vault-4.vault-internal:8200"
            leader_ca_cert_file = "/vault/userconfig/tls-ca/ca.crt"
            leader_client_cert_file = "/vault/userconfig/tls-server/tls.crt"
            leader_client_key_file = "/vault/userconfig/tls-server/tls.key"
          }
        }

        # Auto-unseal with AWS KMS (replace with your KMS configuration)
        seal "awskms" {
          region     = "us-west-2"
          kms_key_id = "your-kms-key-id"
        }

        # Service registration for Kubernetes
        service_registration "kubernetes" {}

        # Telemetry for monitoring
        telemetry {
          prometheus_retention_time = "30s"
          disable_hostname = true
        }

        # Enable auditing
        log_level = "INFO"
        log_format = "json"

  # Anti-affinity to ensure pods run on different nodes
  affinity: |
    podAntiAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        - labelSelector:
            matchLabels:
              app.kubernetes.io/name: vault
              app.kubernetes.io/instance: "vault"
              component: server
          topologyKey: kubernetes.io/hostname

# UI Configuration
ui:
  enabled: true
  serviceType: "ClusterIP"  # Use ClusterIP and expose via Ingress
  externalPort: 8200

# CSI Provider for secret consumption
csi:
  enabled: true
```



#### Vault in VM 
1. Prepare directories
	```bash 
	sudo mkdir -p /opt/vault/{tls,data,logs}
	sudo chown -R vault:vault /opt/vault
	sudo chmod -R 750 /opt/vault
	```
2. Generate TLS certificates
	```bash 
	# Create directory and set ownership
	sudo mkdir -p /opt/vault/tls
	sudo chown -R vault:vault /opt/vault/tls
	sudo chmod 750 /opt/vault/tls
	
	# Generate certificate inside /opt/vault/tls
	sudo openssl req -out /opt/vault/tls/tls.crt \
	  -new -newkey rsa:4096 -nodes -sha256 -x509 \
	  -keyout /opt/vault/tls/tls.key \
	  -subj "/O=HashiCorp/CN=vault.local" \
	  -addext "subjectAltName = IP:127.0.0.1,IP:<YOUR_SERVER_IP>,DNS:vault.local" \
	  -days 3650
	
	# Fix ownership and permissions
	sudo chown vault:vault /opt/vault/tls/tls.*
	sudo chmod 640 /opt/vault/tls/tls.key
	sudo chmod 644 /opt/vault/tls/tls.crt

	```
3. configure vault 
	```bash 
	# sudo vim /etc/vault.d/vault.hcl
	ui = true
	
	storage "file" {
	  path = "/opt/vault/data"
	}
	
	listener "tcp" {
	  address          = "0.0.0.0:8200"
	  tls_cert_file    = "/opt/vault/tls/tls.crt"
	  tls_key_file     = "/opt/vault/tls/tls.key"
	  tls_min_version  = "tls12"
	}
	
	# Recommended cluster settings
	api_addr     = "https://<YOUR_SERVER_IP>:8200"
	cluster_addr = "https://<YOUR_SERVER_IP>:8201"
	
	cluster_name = "vault-cluster"
	```

3. Verify vault installation 
	```bash 
	# Add Certificate 
	sudo cp /opt/vault/tls/tls.crt /usr/local/share/ca-certificates/vault.crt
	sudo update-ca-certificates
	exec $SHELL
	# verfiy 
	export VAULT_ADDR="https://127.0.0.1:8200"
	vault status 
	```
4. Intialize vault server 
	```bash 
	vault operator init 
	```
5. unseal cluster 
	```
	vault operator unseal 
	```







haitham@ship:~$ vault operator init
Unseal Key 1: YRklFkOuVbWDczxoJK2kxSQwBJ06zo4XnTlOeDv0yxqH
Unseal Key 2: l+8vyB4P4YWKiit5D9hEIOzxsElkDjgtYI4fXlSDICP/
Unseal Key 3: 0DC9zZ4tVSZv5ztrzRbcnHdBTGAx7/GubCYoLM0CM3/z
Unseal Key 4: ZSKa/wtclhGwulp3bQNCnopupb1uozlxx5G1Tad6w+OY
Unseal Key 5: abd6IFyJtRws7hZJ8GjsvfJB6/ZYAX0Hucm2WzmxbeGR

Initial Root Token: hvs.2YPr0uJQPsms84gMN6jP3kOT

Vault initialized with 5 key shares and a key threshold of 3. Please securely
distribute the key shares printed above. When the Vault is re-sealed,
restarted, or stopped, you must supply at least 3 of these keys to unseal it
before it can start servicing requests.

Vault does not store the generated root key. Without at least 3 keys to
reconstruct the root key, Vault will remain permanently sealed!

It is possible to generate new unseal keys, provided you have a quorum of
existing unseal keys shares. See "vault operator rekey" for more information.
haitham@ship:~$