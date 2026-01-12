1. Gather require inforamtion (VPC, Subnetes)
	```bash 
	# List All VPCs
	aws ec2 describe-vpcs --region eu-central-1
	# List subnets in your VPC (replace vpc-xxxxx with your VPC ID)
	aws ec2 describe-subnets --filters "Name=vpc-id,Values=vpc-0cee78e4d15e67cbb" --region eu-central-1
	```
2.  Create EFS for sites  (EFS ID from the command output)
	```bash
	aws efs create-file-system \
	  --performance-mode generalPurpose \
	  --throughput-mode bursting \
	  --backup \
	  --tags Key=Name,Value=frappe-sites \
	  --region eu-central-1
	
	```
	```bash 
	aws efs create-file-system \
	  --performance-mode generalPurpose \
	  --throughput-mode bursting \
	  --backup \
	  --tags Key=Name,Value=frappe-logs \
	  --region eu-central-1
	```

3. Create Security group for EFS 
	```bash 
	# Create security group
	aws ec2 create-security-group \
	  --group-name efs-mount-sg \
	  --description "Security group for EFS mount targets" \
	  --vpc-id vpc-0c459bd5f48b34a82 \
	  --region eu-central-1
	```

3. Add inboudn rule for NFS (Port 2049) from your application security group or CIDR 
	```bash 
	aws ec2 authorize-security-group-ingress \
		  --group-id sg-08408590911fe795f \
		  --protocol tcp \
		  --port 2049 \
		  --source-group sg-0bc29eb307891f1f3 \
		  --region eu-central-1
	```
4. Create Mount Targets (ENI in Subnet) for each EFS  
	1. for Sites 
		```bash 
		aws efs create-mount-target \
		  --file-system-id fs-098120c0d6c981458 \
		  --subnet-id subnet-0f5d05469fbb36fd6 \
		  --security-groups sg-08408590911fe795f \
		  --region eu-central-1
		```
	2. for logs 
		```bash 
	    aws efs create-mount-target \
		  --file-system-id fs-017fe0b7ade750aab \
		  --subnet-id subnet-0f5d05469fbb36fd6 \
		  --security-groups sg-08408590911fe795f \
		  --region eu-central-1
		```

#### Prepare my ubuntu machine 
1. install needed tools 
	```bash 
	# Update package list
	sudo apt-get update
	
	# Install the Amazon EFS utilities
	sudo apt-get install -y amazon-efs-utils
	
	# If amazon-efs-utils is not available, install nfs-common
	sudo apt-get install -y nfs-common
	```

1. Create Mount Points 
	```bash 
	# Create directories for mounting EFS
	sudo mkdir -p ~/sites
	sudo mkdir -p ~/logs
	```
2. Mount EFS
	```bash
	# Mount frappe-sites EFS
	sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport fs-098120c0d6c981458.efs.eu-central-1.amazonaws.com:/ ~/sites
	
	# Mount frappe-logs EFS
	sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport fs-017fe0b7ade750aab.efs.eu-central-1.amazonaws.com:/  ~/logs
	```
1. Grant ubuntu user access 
	```bash
	# Change ownership of the sites directory
	sudo chown -R ubuntu:ubuntu ~/sites
	
	# Change ownership of the logs directory
	sudo chown -R ubuntu:ubuntu ~/logs
	
	# Set appropriate permissions
	sudo chmod -R 755 ~/sites
	
	sudo chmod -R 755 ~/logs
	```