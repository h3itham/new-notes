1. usage 
	```bash 
	# Create folder with service name 
	mkidr [service-name]
	./script [service-name]
	```
2. script
	```bash
	#!/bin/bash
	
	# === CONFIGURATION ===
	SERVICE_NAME="${1:-mi-areadriver-dim-1}"  # Default to mi-areadriver-dim-1 if no argument provided
	REGION="${2:-europe-west3}"                # Default to europe-west3 if no argument provided
	
	echo "=== Service: $SERVICE_NAME | Region: $REGION ==="
	
	# Get image
	IMAGE=$(gcloud run services describe $SERVICE_NAME --region=$REGION --format="value(spec.template.spec.containers[0].image)")
	echo "=== Extracting from: $IMAGE ==="
	
	# Pull image with correct platform
	docker pull --platform linux/amd64 $IMAGE
	
	# Extract files
	docker create --platform linux/amd64 --name temp-extract $IMAGE
	docker cp temp-extract:/app ./${SERVICE_NAME}-code
	docker cp temp-extract:/requirements.txt ./requirements.txt 2>/dev/null || echo "No requirements.txt in root"
	docker cp temp-extract:/usr/local/lib/python3.12/site-packages/google ./google-packages 2>/dev/null || echo "No google packages"
	docker rm temp-extract
	
	# Show structure
	echo -e "\n=== File Structure ==="
	find ./${SERVICE_NAME}-code -type f 2>/dev/null || echo "Code directory not found"
	
	# Show Python files
	echo -e "\n=== Python Files Content ==="
	find ./${SERVICE_NAME}-code -name "*.py" -exec echo -e "\n--- {} ---" \; -exec cat {} \; 2>/dev/null
	
	# Show requirements if exists
	echo -e "\n=== Requirements ==="
	cat ./requirements.txt 2>/dev/null || echo "Not found in root"
	
	# Try common locations for requirements
	find ./${SERVICE_NAME}-code -name "requirements.txt" -exec cat {} \; 2>/dev/null
	
	# Check for Pub/Sub patterns
	echo -e "\n=== Searching for Pub/Sub references ==="
	grep -r "pubsub\|Pub/Sub\|push\|subscription\|request\\.data\|request\\.json" ./${SERVICE_NAME}-code 2>/dev/null || echo "No matches found"
	
	# Check installed packages versions
	echo -e "\n=== Checking Google Package Versions ==="
	find ./google-packages -name "__init__.py" -path "*/bigquery/*" 2>/dev/null | head -5
	
	echo -e "\n=== Extraction Complete ==="
	echo "Code extracted to: ./${SERVICE_NAME}-code/"
	```