#### Create App 
1. Create Dummy React app 
	```bash 
	npx create-react-app myapp
	```
2. Dockerfile  
	```Dockerfile 
	# Step 1: Build React app
	FROM node:18 AS build
	WORKDIR /app
	COPY ./myapp/package*.json ./
	RUN npm install
	COPY . .
	RUN npm run build
	
	# Step 2: Serve app using a lightweight server
	FROM gcr.io/distroless/nodejs18-debian11
	WORKDIR /app
	COPY --from=build /app/build /app
	# Use a static file server
	ENTRYPOINT [ "npx", "serve", "-s", "/app", "-l", "8080" ]
	```

#### GCP Part 
1. Enable Cloud Run and Cloud Registry APIs 
	```bash 
	gcloud services enable run.googleapis.com artifactregistry.googleapis.com
	```
2. Create Artifact Registry Repo 
	```bash 
	gcloud artifacts repositories create react-repo \
	  --repository-format=docker \
	  --location=us-central1
	```
3. Create Service Account which have permission needed on Registry and CloudRun service account name `githubaction-cloudrun-sa`
4. Create json key and add it to github Acction, secret name `GCP_CREDENTIALS` 

#### Github Action 

- Create workflow file 
	```bash 
	mkdir -p .github/workflows/
	vim .github/workflows/deployment.yml 
	```
- Create deployment file 
```yaml
name: Deploy React to Cloud Run

on:
  push:
    branches:
      - master  # deploy on push to main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout source
        uses: actions/checkout@v3

      - name: Authenticate to Google Cloud
        uses: google-github-actions/auth@v1
        with:
          credentials_json: ${{ secrets.GCP_CREDENTIALS }}

      - name: Configure Docker for Artifact Registry
        run: gcloud auth configure-docker us-central1-docker.pkg.dev --quiet

      - name: Build and Push Docker image
        run: |
          IMAGE="us-central1-docker.pkg.dev/$PROJECT_ID/react-repo/react-app:${{ github.sha }}"
          docker build -t $IMAGE .
          docker push $IMAGE
          echo "IMAGE=$IMAGE" >> $GITHUB_ENV

      - name: Deploy to Cloud Run
        run: |
          gcloud run deploy react-app \
            --image $IMAGE \
            --region us-central1 \
            --platform managed \
            --allow-unauthenticated
```