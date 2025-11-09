# CI/CD Pipeline Documentation

## Overview

This document outlines the standardized CI/CD pipeline architecture that will be implemented across all projects in our organization. The pipeline follows a dual-environment approach with resource-optimized deployments.
## Architecture Overview

### Environment Strategy

Our CI/CD pipeline is built around two primary environments:

- **Development Environment**: Resource-optimized for testing and development
- **Production Environment**: Full-scale resources for live applications

### Branch Strategy

We follow a simplified Git flow with two main branches:

- **Dev branch**: Development branch for feature integration and testing
- **Prod branch**: Production-ready code for live deployments

## Environment Configuration

### Development Environment
- **Purpose**: Testing, integration, and development work
- **Resources**: 
  - CPU: 0.5-1 vCPU
  - Memory: 512MB-1GB RAM
  - Storage: 10-20GB
- **Scaling**: Limited horizontal scaling
- **Uptime**: Non-critical (can have downtime for maintenance)

### Production Environment
- **Purpose**: Live application serving real users
- **Resources**:
  - CPU: 2-4+ vCPUs
  - Memory: 4-8+ GB RAM
  - Storage: 50-200+ GB
- **Scaling**: Full auto-scaling capabilities
- **Uptime**: High availability (99.9%+ uptime target)
## CI/CD Workflow

```yml 
name: Bluebook Deploy Workflow
on:
  push:
    branches:
      - ${{ vars.DEPLOY_BRANCH }}
env:
  IMAGE_BASE: ${{ vars.IMAGE_BASE }}
  CONTAINER_NAME: ${{ vars.CONTAINER_NAME }}
  REPO_PATH: ${{ vars.REPO_PATH }}
  DOCKER_NETWORK: ${{ vars.DOCKER_NETWORK }}
  CONTAINER_IP: ${{ vars.CONTAINER_IP }}
  ENVIRONMENT_NAME: ${{ vars.ENVIRONMENT_NAME }}
jobs:
  get-sha:
    name: Get Short Commit SHA
    runs-on: ubuntu-latest
    outputs:
      sha_short: ${{ steps.get_sha.outputs.sha_short }}
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      - name: Get short commit SHA
        id: get_sha
        run: echo "sha_short=$(git rev-parse --short HEAD)" >> "$GITHUB_OUTPUT"
  ssh-setup:
    name: SSH Setup Check
    runs-on: ubuntu-latest
    needs: get-sha
    environment: ${{ vars.ENVIRONMENT_NAME }}
    steps:
      - name: SSH key check
        run: echo "$SERVER_SSH_KEY"
        env:
          SERVER_SSH_KEY: ${{ secrets.SERVER_SSH_KEY }}
  git-pull:
    name: Git Pull on Server
    runs-on: ubuntu-latest
    needs: ssh-setup
    environment: ${{ vars.ENVIRONMENT_NAME }}
    steps:
      - name: Git pull via SSH
        uses: appleboy/ssh-action@v1.0.0
        with:
          host: ${{ secrets.SERVER_HOST }}
          username: ${{ secrets.SERVER_USER }}
          key: ${{ secrets.SERVER_SSH_KEY }}
          script: |
            set -e
            cd ${{ env.REPO_PATH }}
            git reset --hard
            git pull origin ${{ vars.DEPLOY_BRANCH || 'prod' }}
  build-image:
    name: Build Docker Image on Server
    runs-on: ubuntu-latest
    needs: [get-sha, git-pull]
    environment: ${{ vars.ENVIRONMENT_NAME }}
    steps:
      - name: Build Docker image via SSH
        uses: appleboy/ssh-action@v1.0.0
        with:
          host: ${{ secrets.SERVER_HOST }}
          username: ${{ secrets.SERVER_USER }}
          key: ${{ secrets.SERVER_SSH_KEY }}
          script: |
            set -e
            cd ${{ env.REPO_PATH }}
            IMAGE_TAG=${{ env.IMAGE_BASE }}:${{ needs.get-sha.outputs.sha_short }}
            echo "Building Docker image: $IMAGE_TAG"
            docker build -t $IMAGE_TAG .
  deploy-container:
    name: Deploy Container on Server
    runs-on: ubuntu-latest
    needs: [get-sha, build-image]
    environment: ${{ vars.ENVIRONMENT_NAME }}
    steps:
      - name: Deploy via SSH
        uses: appleboy/ssh-action@v1.0.0[[Container IPs]]
        with:
          host: ${{ secrets.SERVER_HOST }}
          username: ${{ secrets.SERVER_USER }}
          key: ${{ secrets.SERVER_SSH_KEY }}
          script: |
            set -e
            IMAGE_TAG=${{ env.IMAGE_BASE }}:${{ needs.get-sha.outputs.sha_short }}
            echo "Stopping and removing existing container..."
            docker stop ${{ env.CONTAINER_NAME }} || true
            docker rm -f ${{ env.CONTAINER_NAME }} || true
            echo "Running new container with image: $IMAGE_TAG"
            docker run -itd --name ${{ env.CONTAINER_NAME }} --network ${{ env.DOCKER_NETWORK }} --restart always --ip ${{ env.CONTAINER_IP }}  $IMAGE_TAG
            echo "Deployment successful."
```