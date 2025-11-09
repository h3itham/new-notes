 - Use the following script just change variables. 
	```bash 
	#!/bin/bash
	
	# Configuration
	GITHUB_ORG="Teslm-App"        # Ensure that this is case sensitive
	GITHUB_REPO="teslm-admin"
	GITHUB_ENVIRONMENT="Staging"  # Must match your workflow environment name
	S3_BUCKET="teslm-staging-dashboard"
	AWS_ACCOUNT_ID="242945234432"
	AWS_REGION="eu-central-1"
	ROLE_NAME="GitHubActionsDeployRoleTeslmAdmin"  # Keeping your original name
	
	echo "Setting up GitHub OIDC for AWS..."
	echo "Repository: ${GITHUB_ORG}/${GITHUB_REPO}"
	echo "Environment: ${GITHUB_ENVIRONMENT}"
	echo ""
	
	# 1. Create OIDC Provider (if not exists)
	echo "Creating OIDC provider..."
	aws iam create-open-id-connect-provider \
	  --url https://token.actions.githubusercontent.com \
	  --client-id-list sts.amazonaws.com \
	  --thumbprint-list 6938fd4d98bab03faadb97b34396831e3780aea1 \
	  2>/dev/null || echo "OIDC provider already exists"
	
	# 2. Create trust policy for specific environment
	cat > trust-policy.json << EOF
	{
	  "Version": "2012-10-17",
	  "Statement": [
	    {
	      "Effect": "Allow",
	      "Principal": {
	        "Federated": "arn:aws:iam::${AWS_ACCOUNT_ID}:oidc-provider/token.actions.githubusercontent.com"
	      },
	      "Action": "sts:AssumeRoleWithWebIdentity",
	      "Condition": {
	        "StringEquals": {
	          "token.actions.githubusercontent.com:aud": "sts.amazonaws.com",
	          "token.actions.githubusercontent.com:sub": "repo:${GITHUB_ORG}/${GITHUB_REPO}:environment:${GITHUB_ENVIRONMENT}"
	        }
	      }
	    }
	  ]
	}
	EOF
	
	# 3. Create or update role
	echo "Creating/updating IAM role..."
	aws iam create-role \
	  --role-name $ROLE_NAME \
	  --assume-role-policy-document file://trust-policy.json \
	  --description "Role for GitHub Actions to deploy to S3 and CloudFront for ${GITHUB_ENVIRONMENT} environment" \
	  2>/dev/null || \
	aws iam update-assume-role-policy \
	  --role-name $ROLE_NAME \
	  --policy-document file://trust-policy.json
	
	# 4. Create permissions policy (same as before)
	cat > permissions-policy.json << EOF
	{
	  "Version": "2012-10-17",
	  "Statement": [
	    {
	      "Effect": "Allow",
	      "Action": [
	        "s3:PutObject",
	        "s3:GetObject",
	        "s3:DeleteObject",
	        "s3:ListBucket",
	        "s3:PutBucketWebsite",
	        "s3:GetBucketWebsite",
	        "s3:PutBucketPolicy",
	        "s3:GetBucketPolicy"
	      ],
	      "Resource": [
	        "arn:aws:s3:::${S3_BUCKET}",
	        "arn:aws:s3:::${S3_BUCKET}/*"
	      ]
	    },
	    {
	      "Effect": "Allow",
	      "Action": [
	        "cloudfront:CreateInvalidation",
	        "cloudfront:GetInvalidation",
	        "cloudfront:GetDistribution"
	      ],
	      "Resource": "arn:aws:cloudfront::${AWS_ACCOUNT_ID}:distribution/*"
	    },
	    {
	      "Effect": "Allow",
	      "Action": [
	        "sts:GetCallerIdentity"
	      ],
	      "Resource": "*"
	    }
	  ]
	}
	EOF
	
	# 5. Attach policy
	echo "Attaching permissions policy..."
	aws iam put-role-policy \
	  --role-name $ROLE_NAME \
	  --policy-name GitHubActionsDeployPolicy \
	  --policy-document file://permissions-policy.json
	
	echo "✅ Setup complete!"
	echo "Role ARN: arn:aws:iam::${AWS_ACCOUNT_ID}:role/${ROLE_NAME}"
	echo "Configured for environment: ${GITHUB_ENVIRONMENT}"
	
	echo "Cleaning up temporary files..."
	rm -f trust-policy.json permissions-policy.json
	
	echo "Done! Your GitHub Actions workflow should now work."
	```