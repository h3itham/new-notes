# AWS SSO Authentication Setup Guide
- Open the credential page 
- write the following command 
	```bash 
	aws configure sso 
	```
## Prerequisites
- AWS SSO enabled in your organization
- AWS CLI v2 installed
- Your SSO start URL (format: https://d-xxxxxxxxxx.awsapps.com/start)
- Account ID and role name you want to assume

## Step 1: Configure AWS CLI Profile

Add the following configuration to your `~/.aws/config` file:

```ini
[profile YOUR_PROFILE_NAME]
sso_start_url = YOUR_SSO_START_URL
sso_region = YOUR_SSO_REGION
sso_account_id = YOUR_ACCOUNT_ID
sso_role_name = YOUR_ROLE_NAME
region = YOUR_PREFERRED_REGION
output = json
```

Example for this project:
```ini
[profile 242945234432_AdministratorAccess]
sso_start_url = https://d-99676e9b06.awsapps.com/start
sso_region = eu-central-1
sso_account_id = 242945234432
sso_role_name = AdministratorAccess
region = eu-central-1
output = json
```

## Step 2: Login Using SSO

Run the following command:
```bash
aws sso login --profile YOUR_PROFILE_NAME
```

For this project:
```bash
aws sso login --profile 242945234432_AdministratorAccess
```

This will:
1. Open your default browser
2. Navigate to the AWS SSO login page
3. Prompt you to authenticate
4. Create temporary credentials

## Step 3: Verify Authentication

Verify your identity and permissions:
```bash
aws sts get-caller-identity --profile YOUR_PROFILE_NAME
```

Expected output should look like:
```json
{
    "UserId": "AROAXXXXXXXXXXXXXXXXX:username",
    "Account": "242945234432",
    "Arn": "arn:aws:sts::242945234432:assumed-role/AWSReservedSSO_AdministratorAccess_XXXXX/username"
}
```

## Using with Terraform

### Backend Configuration
```hcl
terraform {
  backend "s3" {
    bucket         = "your-terraform-state-bucket"
    key            = "path/to/state.tfstate"
    region         = "eu-central-1"
    dynamodb_table = "your-lock-table"
    profile        = "242945234432_AdministratorAccess"
  }
}
```

### Provider Configuration
```hcl
provider "aws" {
  region  = var.region
  profile = var.profile  # Set to "242945234432_AdministratorAccess" in terraform.tfvars
}
```

## Troubleshooting

### Common Issues

1. **Missing SSO Configuration**
   ```
   Missing the following required SSO configuration values: sso_start_url, sso_region
   ```
   Solution: Ensure your AWS config file contains all required SSO fields.

2. **Session Expired**
   If you get authentication errors, your SSO session might have expired. Simply run:
   ```bash
   aws sso login --profile YOUR_PROFILE_NAME
   ```

3. **Wrong Role/Account**
   Verify your configuration matches your intended role and account:
   ```bash
   aws sts get-caller-identity --profile YOUR_PROFILE_NAME
   ```

## Session Duration

- AWS SSO sessions typically last 8 hours
- You'll need to re-authenticate using `aws sso login` when the session expires
- Terraform operations will fail with authentication errors when the session expires

## Security Best Practices

1. Never store SSO credentials in your code or version control
2. Use the minimum required permissions for your SSO role
3. Regularly rotate credentials and review access patterns
4. Always verify you're using the correct profile before running destructive operations

## Automation Tips

When scripting with AWS SSO:
1. Always specify the profile explicitly
2. Check authentication status before running critical commands
3. Handle session expiration gracefully in your scripts

Example shell function for checking SSO session:
```bash
check_sso_session() {
    local profile=$1
    if ! aws sts get-caller-identity --profile "$profile" &> /dev/null; then
        echo "SSO session expired. Logging in..."
        aws sso login --profile "$profile"
    fi
}
```