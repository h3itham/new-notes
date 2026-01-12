```bash
#!/bin/bash

# Update the apt package index
echo "Updating the apt package index..."
sudo apt-get update

# Install packages to allow apt to use a repository over HTTPS
echo "Installing packages to allow apt to use a repository over HTTPS..."
sudo apt-get install -y apt-transport-https ca-certificates curl software-properties-common

# Add Docker’s official GPG key
echo "Adding Docker’s official GPG key..."
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

# Set up the stable repository
echo "Setting up the Docker stable repository..."
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"

# Update the apt package index again
echo "Updating the apt package index again..."
sudo apt-get update

# Install the latest version of Docker CE
echo "Installing the latest version of Docker CE..."
sudo apt-get install -y docker-ce

# Verify that Docker CE is installed correctly by running the hello-world image
echo "Verifying the Docker installation..."
sudo docker run hello-world

# Enable Docker to start on boot
echo "Enabling Docker to start on boot..."
sudo systemctl enable docker

# Add the current user to the Docker group (optional)
# This avoids the need to use sudo with Docker commands
echo "Adding the current user to the Docker group..."
sudo usermod -aG docker $USER

# Print completion message
echo "Docker installation completed successfully. You may need to log out and log back in to apply the user group changes."
```