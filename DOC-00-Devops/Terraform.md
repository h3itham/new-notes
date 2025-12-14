> Haitham Is some one who responsible for code after it has been written. 
#### Thinking Process & workflow 
1. Understand Architecture. 
2. Plan your resources 
3. Research Terraform resources you can use (AI here)
4. Implementation 
	1. Enable APIs of all services. 
#### Introduction 
- **Infrastructure as a code** (IaaC): Write and execute code to define, create and destroy infrastructure using code. 
- Terraform Define infrastructure using simple & declarative language. 
- Solving <span style="color:rgb(255, 255, 0)">configuration drift</span> problem "Every Server has different configuration". 
- <span style="color:rgb(255, 0, 0)">What is the benefit of infrastructure as a code?</span> 
	 - Business
		1. Reduce Cost (Pay for one instead of 20).  Reduce Cost. 
		2. Fast Deployment and fast shipping software. 
	- Technical(DevOps Engineer, Developers). 
		1. End Configuration Drift. 
		2. Reusability
		3. Versioning 
		4. Collaboration 
		5. Reduce Human Error 
- what is the difference between infrastructure as a code and configuration management 
	- Infrastructure 
		1. Used to Provision and managing cloud resources. 
	- Configuration management 
		1. Used for virtual machine OS-Level configuration 
- **Idempotent**
  Run Code Once or ten thousand times, you will get the same result. 
- Different between Procedural and Declarative language? 
	- Procedural 
		- Execute code Line By Line 
		- One command for one purpose. 
		- EX "Linux Command Line"

	- Declarative 
		- Programmer determine <span style="color:rgb(0, 255, 0)">what</span> is the desire state, Declarative determine <span style="color:rgb(0, 255, 0)">How</span> to reach to desire state. 
- Terraform Working Concept 
	1. Terraform binary pares code. 
	2. Translate it into a series of API calls to the cloud provider. 

- Authenticate Terraform. 
	- AWS 
		1. Create none root user account. 
		2. Run The following command 
			```bash 
			aws configure k
			```
	- GCP 
- Types of Blocks  
	1. Resources 
		```hcl
		resource "TYPE" "NAME" {
		  # arguments
		}
		
		```
	2. Variables 
		```hcl 
		variable "region" {
		  default = "us-east-1"
		}
		
		```
	3. Outputs 
		```hcl  
		output "instance_ip" {
		  value = aws_instance.example.public_ip
		}
		```
	4. Modules 
		```hcl 
		module "vpc" {
		  source = "./modules/vpc"
		  cidr_block = "10.0.0.0/16"
		}
		
		```



#### Project structure 
- The Following Folders structure is my best structure. 
	```txt 
	📂 terraform-project/
	 ├── 📂 modules/             # Reusable modules
	 │   ├── 📂 networking/
	 │   │   ├── main.tf
	 │   │   ├── variables.tf
	 │   │   └── outputs.tf 
	 │   ├── 📂 compute/
	 │   │   ├── main.tf
	 │   │   ├── variables.tf
	 │   │   └── outputs.tf 
	 │   └── ...
	 ├── 📂 environments/        # Different environments
	 │   ├── 📂 dev/
	 │   │   ├── main.tf         # Calls modules with dev-specific configs
	 │   │   ├── variables.tf
	 │   │   ├── terraform.tfvars
	 │   │   ├── backend.tf
	 │   ├── 📂 prod/
	 │   │   ├── main.tf
	 │   │   ├── variables.tf
	 │   │   ├── terraform.tfvars
	 │   │   ├── backend.tf
	 │   └── ...
	 ├── 📂 scripts/             # Automation scripts
	 │   ├── init.sh
	 │   ├── apply.sh
	 │   ├── destroy.sh
	 ├── .terraform.lock.hcl      # Terraform lock file
	 ├── .gitignore               # Ignore sensitive files
	 ├── provider.tf              # Global provider config
	 ├── versions.tf              # Define Terraform version constraints
	 ├── README.md                # Project documentation
	
	```

#### Provider
- Provider: Plugins that allow you to interact with APIs. 
	- Cloud Services (AWS, GCP and Azure). 
	- Software as a service (Github)
	- Other API (kubernetes, postgres)
- You can write more than one and choose between them 
	```hcl
	provider "aws" {
	    alias = "provider_1"
	    region = "us-east-1"
	    }
	provider "aws" { 
	    alisas = "provider_2"
		region = "us-west-1"
	   }
	```

#### Resources 
-  Is the infrastructure objects you create (EC2 instances, )
 
	```bash
	resource "aws_instance" "web" {
	  ami           = "ami-0c55b159cbfafe1f0"
	  instance_type = "t2.micro"
	}
	```
- Affect of `terraform apply` on resources 
	- `+` Create new resource 
	- `-` Delete resource 
	- `~` Update resource in place 



#### Variables 
Used to make your infrastructure configuration more flexible and reusable. 
- **Declaration** with the `variable` block. 
	```bash 
	variable "region" {
	  description = "The AWS region where resources will be created"
	  type        = string
	  default     = "us-east-1"
	}
	
	```

- **Types** 
	- `string`: A single string value.
	- `number`: Numeric values (integers or floats).
	- `bool`: Boolean values (`true` or `false`).
	- `list`: A list of values (can be of any type).
	- `map`: A key-value pair, similar to a dictionary.
	- `object`: A complex structure with named attributes.
	- `any` accept any type. 

- Assign values (Four methods)
	1. Using Default. 
	2. Command line while apply 
		```bash 
		terraform apply -var "region=us-east-1"
		```
	3. Environment variables. `TF_VAR_region=us-west-2` 
	4. Variables definition files `terraform.tfvars`

- Accessing Values 
	You can reference variables using `var.` 
	```hcl 
	resource "aws_instance" "example" {
	  ami           = "ami-123456"
	  instance_type = "t2.micro"
	  availability_zone = var.availability_zones[0]  # Accessing the first AZ from the list
	}

	```

- **Variables Order** while run time. 
	1. Environment Variable. 
	2. terraform.tfvars 
	3. terraform.tfvars.json 
	4. *.auto.tfvars or *.auto.tfvars.json 

- **Data Sources** Use information defined outside terraform you can import or use resources that are already exists.
#### Local  = Local Value  
- All coding languages have a fundamentsl concept of "variables" and "socpe" and terraform have. 
- Think of them as variables that are only used within the configuration file or module. 
- Examples 
	```hcl 
	locals {
	  region = "us-west-2"
	  instance_type = "t3.micro"
	}
	
	resource "aws_instance" "example" {
	  ami           = "ami-0c55b159cbfafe1f0"
	  instance_type = local.instance_type
	  availability_zone = "${local.region}a"
	}
	
	```
	
	```hcl
	locals {
	  env        = "dev"
	  project    = "myapp"
	  name_prefix = "${local.project}-${local.env}"
	}
	
	resource "aws_s3_bucket" "bucket" {
	  bucket = "${local.name_prefix}-bucket"
	}

    ```
#### Dynamic 
- Used to reduce number of lines of code
- dynamically generate repeatable nested blocks. 
#### Resource Meta Arguments. 
Are special arguments that can be used with resource block to control how resource are created, updated, or destroyed. 
- **count**:
	Allow you to create multiple instance from the same resource based on numeric value
	```hcl 
	resource "aws_instance" "web" {
	  count         = 3
	  ami           = "ami-12345678"
	  instance_type = "t2.micro"
	
	  tags = {
	    Name = "web-server-${count.index}"
	  }
	}
	```

- **for_each** 
	Allows you to create multiple instances of a resource based on a map or set of strings.
	```hcl 
	resource "aws_instance" "web" {
	  for_each = {
	    "web-1" = "ami-12345678"
	    "web-2" = "ami-87654321"
	  }
	
	  ami           = each.value
	  instance_type = "t2.micro"
	
	  tags = {
	    Name = each.key
	  }
	}
	```

- **depends_on** 
	Terraform is declarative which mean it define the dependencies, but for time matter you can use this argument. 
	```hcl
	resource "aws_instance" "web" {
	  ami           = "ami-12345678"
	  instance_type = "t2.micro"
	  depends_on = [aws_s3_bucket.example]
	resource "aws_s3_bucket" "example" {
	  bucket = "my-example-bucket"
	}
	```

- **Provider** 
	Allow you to chose a non default provider for resource 
	```hcl 
	provider "aws" {
	  region = "us-east-1"
	}
	provider "aws" {
	  alias  = "west"
		  region = "us-west-1"
	}
	resource "aws_instance" "web" {
	  provider = aws.west
	  ami           = "ami-12345678"
	  instance_type = "t2.micro"
	}
	```

- **lifecycle**
	Terraform manages the lifecycle of a resource. It includes several nested arguments:
	- **`create_before_destroy`**: Ensures a new resource is created before the old one is destroyed.
	- **`prevent_destroy`**: Prevents the resource from being destroyed.
	- **`ignore_changes`**: Ignores changes to specific attributes during updates.
	```hcl 
	resource "aws_instance" "web" {
	  ami           = "ami-12345678"
	  instance_type = "t2.micro"
	  lifecycle {
	    create_before_destroy = true
	    ignore_changes        = [tags]
		  }
	}
	```

- provisioner
	Allow you to run commands or scripts against your resources. after provision them 
	```hcl 
	resource "aws_instance" "web" {
	  ami           = "ami-12345678"
	  instance_type = "t2.micro"
	  provisioner "local-exec" {
		    command = "echo ${self.private_ip} > instance_ip.txt"
		  }
	}
	```


#### Secret File 
- Local API or Terraform special API. 
- Locally is `terraform.tfstate` 
- to read statefile 
	```bash 
	terraform show 
	```
- Convert it to be JSON 
	```bash 
	terrafom show json > tfstate.json
	```

#### Important Terraform commands 

1. Download All plugins and Init 
	```bash 
	terraform init 
	```
2. List all provider in your code 
	```bash 
	terrafrom providers 
	```
3. Pull state files from remote state storage. 
	```bash 
	terraform state pull 
	```
4. List all resources created by terraform 
	```bash 
	terraform state list 
	```
5. Detail information about specific resouce
	```bash 
	terraform state show [resource_name]
	```
6. Import non terraform create resources. 
	```bash 
	terraform import [Terraform_Resouce_Name] -i < resouceid> 
	terraform  aw_instance.name -i 1234567890abcdef0
	```
7. Prevent Specific resource from deletion 
	```bash 
	terrafrom state rm [ResouceName]
	```
8. Rename resources in state file 
	```bash 
	terraform state mv [odl_resouce_name] [new_resouce_name]
	```
9. Automatically format terraform files 
	```bash 
	terraform fmt 
	```
10. To manage different state files you can create more than one workspace 
	```bash 
	# Create worksapce 
	terraform worksapce dev
	terraform worksapce prod 
	terraform worksapce test 
	# Select specific workspace
	terraform worksapce select [space_name]
	```
#### Import Resources 
1. `terraform import` tel terraform to take control of an existing resource
2. Command 
	```bash 
	terraform import resource_type.resource_name 
	```
#### Terraform state 
Every time you run terraform, it records information about what infrastructure it created in a Terraform state file which name is `terraform.tfstate`. <span style="color:rgb(0, 255, 0)">You can consider state file as Private API </span>
Terraform problems when you work with a team. 
1. Shared storage for state files. 
2. Locking state File. 
3. Isolated state files. (work on test environment don't affect production). 
**S3 as Backend**
- Define provider
	```hcl 
	provider "aws" {
	    region = "us-east-1"
	    }
	```
- Backend 
	```bash 
	# Create s3 bucket 
	resource "aws_s3_bucket" "TerraformBackend-S3" {
	          bucket = "TerraformBackend-S3"
	# Prevent accidental deletion of this S3 bucket, Of course, if you really mean to delete it, you can just comment that setting out
	lifecycle {
	prevent_destroy = true
	       	   }
		}	
	# Enable versioning so you can see the full revision history of your state files
	resource "aws_s3_bucket_versioning" "versioning-enabled" {
	      bucket = aws_s3_bucket.TerraformBackend-S3.id
	      versioning_configuration {
	       status = "Enabled"
	       	  }
	   }
	
	# Enable server-side encryption by default
	resource "aws_s3_bucket_server_side_encryption_configuration" "default" {
	    bucket = aws_s3_bucket.TerraformBackend-S3.id
	    rule {
	  	  apply_server_side_encryption_by_default {
	    	 sse_algorithm = "AES256"
	   }
	  }
	 }
	
	# Explicitly block all public access to the S3 bucket
	resource "aws_s3_bucket_public_access_block" "public_access" {
		bucket = aws_s3_bucket.Haitham_Team-state.id
		block_public_acls  = true
		block_public_policy= true
		ignore_public_acls = true
		restrict_public_buckets = true
	}
	# building dynamodb table 
	resource "aws_dynamodb_table" "TerraformBackendLock" {
		name = "TerraformBackendLock"
		billing_mode = "PAY_PER_REQUEST"
		hash_key = "LockID"
		attribute {
			name = "LockID"
		    type = "S"
	    }
	}
	```
- Run `terraform init` to download the provider code, and then run `terraform apply` to deploy. 

- Configure terraform to store the state in you s3 bucket. 
	```hcl
	terraform {
		backend "s3" {
		# Replace this with your bucket name!
			bucket = "TerraformBackend-S3"
			key = "global/s3/terraform.tfstate"
	        region = "us-east-1"
		# Replace this with your DynamoDB table name!
			dynamodb_table = "TerraformBackendLock"
			encrypt	= true
		}
	}
	```


#### Secretes Management with terraform. 
- If you build software it's your responsibility to keep secretes secure. 
- Types of secretes that you can use 
	1. Database Passwords. 
	2. API Keys. 
	3. TLS certifications, ssh keys & GPG keys. 
- Don't store secretes in clear text. 
- Use Environment variables. 
	- `vars.tf` file 
		```hcl 
		variable "username" { 
		    description = "Database Username"
		    type        = string
		    sensitive   = true 
		    }
		variable "password" { 
		    description = "Database password "
		    type        = string
		    sensitive   = true 
		    }
		```

	- `main.tf` file 
		```hcl 
		resource "aws_db_instance" "Testing-DB" {
				identifier_prefix = "terraform-up-and-running"
				engine            = "mysql"
				allocated_storage = 10
				instance_class    = "db.t2.micro"
				skip_final_snapshot = true
				db_name           = var.db_name
				# How to set these parameters securely?
				username          = var.username
				password          = var.password
		}
		```

	- Run the following commands as environment variable
		```bash
		" "export TF_VAR_username="root"
		" "export TF_VAR_password="devops123"
		```
		<span style="color:rgb(255, 0, 0)">Note</span> that the command which start with space dose not added in the history file. 

#### Terraform Modules 
- Terraform **modules** are reusable, self-contained packages of Terraform configurations that allow you to abstract infrastructure components. 
- Help in 
	1. Reusability (avoid copy - pasting code). 
	2. Maintainability (centralized updates). 
	3. Abstraction (Centralized updates). 
	4. collaboration (share modules across teams)
- Types of modules 
	- Root Module 
		- The main directory where you run `terraform apply`.
		- contains `main.tf`, `variables.tf`, `outputs.tf`. 
	- child module 
		- A reusable module called by the root or another module. 
- Module structure 
	```bash 
	📂 modules/
	  📂 ec2-instance/
	    ├── main.tf       # Resources
	    ├── variables.tf  # Input variables
	    ├── outputs.tf    # Output values
	    └── README.md     # Documentation
	```

#### Tips and tricks
- Flatten: function is used to take a list of lists (a nested list) and turn it into a single, flat list.
```hcl
nested = [
  [1, 2],
  [3, 4],
  [5]
]
flatten(nested)
[1,2,3,4,5]
```
#### Null Resource 
- Special resource type that manages **nothing** in cloud provider. 
#### Terraform with GCP 
- Authenticate gcloud with GCP 
	```bash 
	glcoud auth login 
	```
- To test that you have access 
	```bash 
	gcloud projects list 
	```

