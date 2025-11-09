- Install sops
	```bash
		# Download the latest release from GitHub
		curl -LO https://github.com/mozilla/sops/releases/download/v3.7.3/sops-v3.7.3.linux.amd64
		
		# Make it executable
		chmod +x sops-v3.7.3.linux.amd64
		
		# Move to your path
		sudo mv sops-v3.7.3.linux.amd64 /usr/local/bin/sops
		
		# Verify installation
		sops --version
	```


- Install AGE 
	```bash 
		# Download the latest release
		curl -LO https://github.com/FiloSottile/age/releases/download/v1.1.1/age-v1.1.1-linux-amd64.tar.gz
		
		# Extract the archive
		tar xvf age-v1.1.1-linux-amd64.tar.gz
		
		# Move the binaries to your path
		sudo mv age/age /usr/local/bin/
		sudo mv age/age-keygen /usr/local/bin/
		
		# Verify installation
		age --version
		age-keygen --version
	```

- Generate Age Key 
	```bash 
	# Create a directory for your keys
	mkdir -p ~/.age
	
	# Generate a key pair
	age-keygen -o ~/.age/key.txt
	
	# Set proper permissions
	chmod 600 ~/.age/key.txt
	```
- Configure Sops with Age.
	```bash
	# Extract the public key from your key file AGE_PUBLIC_KEY=$(grep "public key:" ~/.age/key.txt | cut -d' ' -f4)
	# Create .sops.yaml in your project directory 
	#vim .sops.yaml
	```

- Add the following content on this file 
	```bash
	creation_rules: - path_regex: .*\.yaml$ age: "${AGE_PUBLIC_KEY}"
	```

- Encrypt yaml file with sops 
```bash
# Encrypt a file
sops --encrypt --in-place config.yaml

# Create and encrypt a new file
sops config.secret.yaml
```