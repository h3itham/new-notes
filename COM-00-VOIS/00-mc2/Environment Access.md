#### Dev Pod 
- Install gcloud
- Authenticate  
    - gcloud auth login   
    - gcloud auth application-default login
-  Run GOCD pipeline to create DEV pod
	```txt 
	https://beta-ne-gocd.neuron.bdp.vodafone.com/go/pipelines#!/
	```
- Get the context for the cluster
	- For beta
		```bash 
		gcloud container clusters get-credentials platform-dev --zone europe-west1 --project vf-grp-clouddmz-lab
		```
	- For Zeta 
		```bash 
		gcloud container clusters get-credentials platform-dev --zone europe-west1 --project vf-nrn-ca-openlab
		```
1. Connect to the container in the pod
	- From command line 
		```bash 
		kubectl -n haitham-shabaan exec -ti haitham-shabaan -c dev-vm -- /bin/bash
		```
	- From vscode kubernetes extension 
2. Authenticate with Google from Dev Pod 
	```bash 
	gcloud auth login --no-launch-browser
	```
	```bash 
	gcloud auth application-default login --no-launch-browser
	```
	```bash
	gcloud auth application-default login --scopes="https://www.googleapis.com/auth/cloud-platform"
	```
	
3. Clone all projects 
	- Permanently store github credential 
		```bash
		git config --global credential.helper store
		```
	- Project configs 
		```bash 
		git clone https://github.vodafone.com/VFGroup-CloudAnalytics/neuron-gcp-project-configs.git
		```
	- Platform 
		```bash 
		git clone https://github.vodafone.com/VFGroup-CloudAnalytics/neuron-gcp-platform.git
		```
	- Modules 
		```bash 
		git clone https://github.vodafone.com/VFGroup-CloudAnalytics/terraform-modules.git
		```
		
4. Change Project or switch to new project 
	```bash 
	switch_project.sh -p vf-mc2dev-ca-nonlive.yaml
	```
	This will downlaod all project configs 
5. You can't run apply before run plan 
#### Tmux implementation 
- This is script used as `tmux` tool which I build it to run every plan and apply on the background and don't distrirb any thing. 
- script [[tmux implementaion]]

## Create Local pod 
1. Configure your docker with the remote one 
	```bash 
	gcloud auth configure-docker europe-west1-docker.pkg.dev
	```

2. Run docker container 
	```bash 
	docker run -d -t --platform linux/amd64 --name dev-pod europe-west1-docker.pkg.dev/vf-grp-neuronenabler-nonlive/core-dev-pod/core-dev-pod:sdk-533.0.0-rc
	```
3. Add proxy to .bashrc file 
	```bash
	export HTTPS_PROXY=http://195.233.9.60:8080
	export HTTP_PROXY=http://195.233.9.60:8080
	export http_proxy=http://195.233.9.60:8080
	export https_proxy=http://195.233.9.60:8080
	```

4. Upgrade error 
	```bash
	export GO_PIPELINE_NAME="local"
	./platform/scripts/terraform-plan.sh -p
	```
5. Show current branch inside terminal 
	```bash 
	# Show current Git branch in terminal prompt matching your color scheme
	parse_git_branch() {
	  git branch 2>/dev/null | sed -n '/\* /s///p' | awk '{print "(" $1 ")"}'
	}
	
	# Color codes:
	# \033[1;32m = bright green (for user@host)
	# \033[1;34m = bright blue (for path)
	# \033[0;31m = red (for git branch)
	# \033[00m = reset colors
	
	export PS1='\[\033[1;32m\]\u@\h\[\033[00m\]:\[\033[1;34m\]\w\[\033[00m\] \[\033[0;31m\]$(parse_git_branch)\[\033[00m\] \$ '
	```

6. Plan specific module 
	```bash 
	./platform/scripts/terraform-plan.sh -p vf-grp-mirep-dev  -- -target=module.composer_v2
	```


#### DevPod script automation 
1. One script  
	```bash 
	cat > weekly_setup.sh << 'EOF'
	#!/bin/bash
	
	# Weekly automation script for pod setup
	# Usage: ./weekly_setup.sh
	
	set -e  # Exit on any error
	
	# Colors for output
	RED='\033[0;31m'
	GREEN='\033[0;32m'
	YELLOW='\033[1;33m'
	NC='\033[0m' # No Color
	
	echo -e "${GREEN}=== Weekly Pod Setup Script ===${NC}"
	
	# Ask for GitHub token
	read -s -p "Enter your GitHub token: " GITHUB_TOKEN
	echo
	
	if [ -z "$GITHUB_TOKEN" ]; then
	    echo -e "${RED}Error: GitHub token is required${NC}"
	    exit 1
	fi
	
	# Function to print step
	print_step() {
	    echo -e "\n${YELLOW}>>> $1${NC}"
	}
	
	# Step 1: Google Cloud Authentication
	print_step "Step 1: Setting up Google Cloud authentication"
	echo "Please follow the authentication prompts..."
	
	echo "Running: gcloud auth login --no-launch-browser"
	gcloud auth login --no-launch-browser
	
	echo "Running: gcloud auth application-default login --no-launch-browser"
	gcloud auth application-default login --no-launch-browser
	
	echo "Running: gcloud auth application-default login with cloud platform scope"
	gcloud auth application-default login --scopes="https://www.googleapis.com/auth/cloud-platform"
	
	# Step 2: Clone repositories
	print_step "Step 2: Cloning repositories"
	
	# Create working directory if it doesn't exist
	WORK_DIR="$(pwd)/neuron-workspace"
	mkdir -p "$WORK_DIR"
	cd "$WORK_DIR"
	
	# Function to clone or update repository
	clone_or_update() {
	    local repo_url="$1"
	    local repo_name="$2"
	    
	    if [ -d "$repo_name" ]; then
	        echo "Directory $repo_name exists, pulling latest changes..."
	        cd "$repo_name"
	        git pull origin main || git pull origin master
	        cd ..
	    else
	        echo "Cloning $repo_name..."
	        # Use token in URL for authentication
	        local auth_url=$(echo "$repo_url" | sed "s|https://|https://$GITHUB_TOKEN@|")
	        git clone "$auth_url"
	    fi
	}
	
	# Clone Project configs
	echo "Cloning neuron-gcp-project-configs..."
	clone_or_update "https://github.vodafone.com/VFGroup-CloudAnalytics/neuron-gcp-project-configs.git" "neuron-gcp-project-configs"
	
	# Clone Platform
	echo "Cloning neuron-gcp-platform..."
	clone_or_update "https://github.vodafone.com/VFGroup-CloudAnalytics/neuron-gcp-platform.git" "neuron-gcp-platform"
	
	# Clone Modules
	echo "Cloning terraform-modules..."
	clone_or_update "https://github.vodafone.com/VFGroup-CloudAnalytics/terraform-modules.git" "terraform-modules"
	
	print_step "Setup completed successfully!"
	echo -e "${GREEN}All repositories are ready in: $WORK_DIR${NC}"
	echo -e "${GREEN}Google Cloud authentication is configured${NC}"
	
	# Optional: List the cloned directories
	echo -e "\nCloned repositories:"
	ls -la "$WORK_DIR"
	EOF
	
	chmod +x weekly_setup.sh && ./weekly_setup.sh
	```