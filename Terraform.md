# Terraform AWS Infrastructure

## Introduction

Terraform is an Infrastructure as Code (IaC) tool that allows DevOps engineers to create, manage, update and destroy cloud infrastructure using configuration files.

Instead of manually creating AWS resources from the AWS Console, we can define infrastructure as code and manage it through Git.

Terraform helps us build infrastructure that is:::::
 
- Automated
- Repeatable
- Version controlled
- Scalable
- Consistent
- Easy to maintain
- Easy to review
- Easy to integrate with CI/CD

In this guide, we will cover Terraform commands, AWS provider configuration, variables, outputs, state management, resources, data sources, modules, remote state and a basic AWS infrastructure example.

---

# Terraform Architecture

```text
Terraform Configuration
        |
        v
terraform init
        |
        v
terraform validate
        |
        v
terraform plan
        |
        v
terraform apply
        |
        v
AWS Infrastructure
        |
        v
terraform destroy
```

---

# Prerequisites

Before using Terraform with AWS, install:

- Terraform
- AWS CLI
- Git
- AWS account

Verify Terraform:

```bash
terraform version
```

Verify AWS CLI:

```bash
aws --version
```

Configure AWS credentials:

```bash
aws configure
```

It will ask for:

```text
AWS Access Key ID
AWS Secret Access Key
Default region name
Default output format
```

Check AWS identity:

```bash
aws sts get-caller-identity
```

---

# Terraform File Structure

A basic Terraform project can look like this:

```text
terraform-aws-project/
│
├── provider.tf
├── main.tf
├── variables.tf
├── outputs.tf
├── terraform.tfvars
├── versions.tf
├── .gitignore
└── README.md
```

---

# Terraform Provider

Create `provider.tf`.

```hcl
provider "aws" {
  region = "ap-south-1"
}
```

For example, `ap-south-1` represents the Mumbai AWS region.

---

# Terraform Versions

Create `versions.tf`.

```hcl
terraform {
  required_version = ">= 1.5.0"

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}
```

---

# Terraform Initialization

The first command we normally run is:

```bash
terraform init
```

This command:

- Initializes the Terraform working directory
- Downloads required providers
- Creates the `.terraform` directory
- Downloads provider plugins
- Configures backend if defined

---

# Terraform Format

Use:

```bash
terraform fmt
```

This formats Terraform configuration files according to Terraform's standard formatting.

To format all files recursively:

```bash
terraform fmt -recursive
```

---

# Terraform Validation

Before creating infrastructure, validate the configuration:

```bash
terraform validate
```

This checks whether the Terraform configuration is syntactically valid and internally consistent.

Expected result:

```text
Success! The configuration is valid.
```

---

# Terraform Plan

Run:

```bash
terraform plan
```

Terraform compares the current configuration with the existing state and shows what changes will be made.

Example:

```text
Plan: 2 to add, 0 to change, 0 to destroy.
```

The plan allows us to review infrastructure changes before applying them.

---

# Terraform Apply

To create infrastructure:

```bash
terraform apply
```

Terraform will show the execution plan and ask for confirmation.

Enter:

```text
yes
```

You can also automatically approve:

```bash
terraform apply -auto-approve
```

Use `-auto-approve` carefully, especially in production.

---

# Terraform Destroy

To delete infrastructure:

```bash
terraform destroy
```

Terraform will display the resources that will be removed.

To automatically approve:

```bash
terraform destroy -auto-approve
```

Never run destroy blindly in production.

---

# Creating an AWS EC2 Instance

Example `main.tf`:

```hcl
resource "aws_instance" "web" {
  ami           = "ami-xxxxxxxxxxxxxxxxx"
  instance_type = "t2.micro"

  tags = {
    Name = "terraform-web-server"
  }
}
```

Initialize:

```bash
terraform init
```

Validate:

```bash
terraform validate
```

Format:

```bash
terraform fmt
```

Review:

```bash
terraform plan
```

Create:

```bash
terraform apply
```

---

# Terraform Variables

Variables help us avoid hardcoding values.

Create `variables.tf`:

```hcl
variable "aws_region" {
  description = "AWS region"
  type        = string
  default     = "ap-south-1"
}

variable "instance_type" {
  description = "EC2 instance type"
  type        = string
  default     = "t2.micro"
}
```

Use the variable:

```hcl
provider "aws" {
  region = var.aws_region
}

resource "aws_instance" "web" {
  ami           = "ami-xxxxxxxxxxxxxxxxx"
  instance_type = var.instance_type

  tags = {
    Name = "terraform-web"
  }
}
```

---

# terraform.tfvars

Create:

```text
terraform.tfvars
```

Example:

```hcl
aws_region    = "ap-south-1"
instance_type = "t2.micro"
```

Terraform automatically loads values from:

```text
terraform.tfvars
```

You can also specify a custom variable file:

```bash
terraform apply -var-file="production.tfvars"
```

---

# Passing Variables from Command Line

We can also pass variables using:

```bash
terraform plan -var="instance_type=t3.micro"
```

Apply:

```bash
terraform apply -var="instance_type=t3.micro"
```

---

# Terraform Outputs

Outputs allow us to display useful information after deployment.

Create `outputs.tf`:

```hcl
output "instance_id" {
  value = aws_instance.web.id
}

output "public_ip" {
  value = aws_instance.web.public_ip
}
```

After applying:

```bash
terraform apply
```

Check outputs:

```bash
terraform output
```

Check a specific output:

```bash
terraform output public_ip
```

---

# Terraform State

Terraform maintains infrastructure information inside a state file.

Default state file:

```text
terraform.tfstate
```

The state allows Terraform to understand:

```text
Configuration
      |
      v
Terraform State
      |
      v
Real AWS Infrastructure
```

Important state commands:

```bash
terraform state list
```

Shows resources tracked by Terraform.

```bash
terraform state show aws_instance.web
```

Shows detailed information about a resource.

```bash
terraform state pull
```

Downloads the current state.

```bash
terraform state rm aws_instance.web
```

Removes a resource from Terraform state without destroying the actual infrastructure.

Use state commands carefully.

---

# Terraform Show

To inspect the current state:

```bash
terraform show
```

To inspect a saved plan:

```bash
terraform show tfplan
```

---

# Terraform Refresh

Modern Terraform workflows generally reconcile state during planning and application.

You may still see:

```bash
terraform refresh
```

in older workflows, but it is not normally required for modern Terraform usage.

A safer approach is often:

```bash
terraform plan
```

---

# Terraform Import

If an AWS resource already exists and we want Terraform to manage it, we can import it.

Example:

```bash
terraform import aws_instance.web i-0123456789abcdef0
```

After importing, inspect:

```bash
terraform state show aws_instance.web
```

Then update the Terraform configuration so it accurately represents the imported infrastructure.

Important:

`terraform import` imports the resource into state. It does not automatically generate a complete Terraform configuration for the resource.

---

# Terraform Data Sources

Data sources allow Terraform to retrieve existing AWS information.

Example:

```hcl
data "aws_ami" "amazon_linux" {
  most_recent = true

  owners = ["amazon"]

  filter {
    name   = "name"
    values = ["al2023-ami-*-x86_64"]
  }
}
```

Use it:

```hcl
resource "aws_instance" "web" {
  ami           = data.aws_ami.amazon_linux.id
  instance_type = "t2.micro"
}
```

---

# Terraform Locals

Locals allow us to define reusable values.

```hcl
locals {
  environment = "dev"

  common_tags = {
    Environment = "dev"
    ManagedBy   = "Terraform"
    Project     = "AWS-DevOps"
  }
}
```

Use:

```hcl
resource "aws_instance" "web" {
  ami           = "ami-xxxxxxxxxxxxxxxxx"
  instance_type = "t2.micro"

  tags = local.common_tags
}
```

---

# Terraform Resource Dependencies

Terraform automatically creates dependency relationships.

Example:

```hcl
resource "aws_security_group" "web" {
  name = "web-sg"
}

resource "aws_instance" "web" {
  ami           = "ami-xxxxxxxxxxxxxxxxx"
  instance_type = "t2.micro"

  security_groups = [aws_security_group.web.name]
}
```

The EC2 instance depends on the security group.

Terraform understands this dependency automatically.

---

# Explicit Dependency

Sometimes we need:

```hcl
depends_on = [
  aws_security_group.web
]
```

Example:

```hcl
resource "aws_instance" "web" {
  ami           = "ami-xxxxxxxxxxxxxxxxx"
  instance_type = "t2.micro"

  depends_on = [
    aws_security_group.web
  ]
}
```

---

# Terraform Count

`count` can create multiple resources.

```hcl
resource "aws_instance" "web" {
  count = 2

  ami           = "ami-xxxxxxxxxxxxxxxxx"
  instance_type = "t2.micro"

  tags = {
    Name = "web-${count.index}"
  }
}
```

Create:

```bash
terraform apply
```

This creates two instances.

---

# Terraform For Each

`for_each` is useful when creating resources from a map or set.

```hcl
variable "instances" {
  default = {
    web = "t2.micro"
    app = "t3.micro"
  }
}
```

Resource:

```hcl
resource "aws_instance" "server" {
  for_each = var.instances

  ami           = "ami-xxxxxxxxxxxxxxxxx"
  instance_type = each.value

  tags = {
    Name = each.key
  }
}
```

---

# Terraform Conditional Expression

Example:

```hcl
variable "environment" {
  default = "dev"
}

locals {
  instance_type = var.environment == "prod" ? "t3.medium" : "t2.micro"
}
```

This allows different infrastructure configurations based on the environment.

---

# Terraform Modules

Modules allow us to create reusable Terraform code.

Example structure:

```text
terraform-project/
│
├── main.tf
├── variables.tf
├── outputs.tf
│
└── modules/
    ├── vpc/
    │   ├── main.tf
    │   ├── variables.tf
    │   └── outputs.tf
    │
    └── ec2/
        ├── main.tf
        ├── variables.tf
        └── outputs.tf
```

Call a module:

```hcl
module "vpc" {
  source = "./modules/vpc"

  environment = "dev"
}
```

Modules are useful for:

- Reusability
- Standardization
- Large infrastructure projects
- Multi-environment deployments
- Team collaboration

---

# Terraform Workspaces

List workspaces:

```bash
terraform workspace list
```

Create workspace:

```bash
terraform workspace new dev
```

Create another:

```bash
terraform workspace new prod
```

Switch workspace:

```bash
terraform workspace select dev
```

Check current workspace:

```bash
terraform workspace show
```

Delete workspace:

```bash
terraform workspace delete dev
```

Workspaces can be useful in some scenarios, but many production teams prefer separate directories or separate state configurations for strong environment isolation.

---

# Terraform Backend

For team environments, storing Terraform state locally is not recommended.

We can use an S3 backend.

Example:

```hcl
terraform {
  backend "s3" {
    bucket = "my-terraform-state-bucket"
    key    = "dev/terraform.tfstate"
    region = "ap-south-1"
  }
}
```

Then:

```bash
terraform init
```

Terraform will configure the backend.

For production, the state bucket should be properly secured with encryption, restricted IAM permissions, versioning and appropriate access controls.

---

# Remote State

Remote state allows multiple engineers and CI/CD systems to work with the same infrastructure state.

Common AWS setup:

```text
Terraform
    |
    v
S3 Backend
    |
    v
Terraform State
```

Depending on the architecture and Terraform version, state locking can be configured using supported backend locking mechanisms.

---

# Terraform Plan File

Create a plan file:

```bash
terraform plan -out=tfplan
```

Review:

```bash
terraform show tfplan
```

Apply exactly that plan:

```bash
terraform apply tfplan
```

This is useful in CI/CD pipelines because the plan can be reviewed before applying infrastructure changes.

---

# Terraform Target

Terraform supports targeted operations:

```bash
terraform plan -target=aws_instance.web
```

Apply targeted resource:

```bash
terraform apply -target=aws_instance.web
```

Targeting should generally be used only for exceptional troubleshooting or recovery scenarios rather than normal infrastructure management.

---

# Terraform Taint and Replace

Older Terraform workflows used:

```bash
terraform taint aws_instance.web
```

Modern Terraform recommends:

```bash
terraform apply -replace="aws_instance.web"
```

Example:

```bash
terraform plan -replace="aws_instance.web"
```

This tells Terraform to replace a specific resource.

---

# Terraform Graph

Terraform can generate a dependency graph:

```bash
terraform graph
```

Example:

```bash
terraform graph > graph.dot
```

The graph can then be visualized using Graphviz.

---

# Terraform Providers

List provider information:

```bash
terraform providers
```

This helps identify which providers are being used by the configuration.

---

# Terraform Lock File

Terraform creates:

```text
.terraform.lock.hcl
```

This file records selected provider versions and checksums.

It should normally be committed to Git.

---

# .gitignore

Do not commit sensitive Terraform files.

Example `.gitignore`:

```text
.terraform/
*.tfstate
*.tfstate.*
*.tfvars
*.tfplan
crash.log
crash.*.log
```

Be careful with `*.tfvars`: if a variable file contains only non-sensitive configuration, you may choose to commit it. Never commit credentials or secrets.

---

# Terraform Sensitive Variables

Example:

```hcl
variable "db_password" {
  type      = string
  sensitive = true
}
```

However, marking a variable as sensitive does not mean the secret is completely absent from Terraform state.

For production environments, consider AWS Secrets Manager or another secure secret-management solution.

---

# Terraform AWS VPC Example

A typical AWS architecture can look like:

```text
                    AWS VPC
                      |
          +-----------+-----------+
          |                       |
      Public Subnet          Private Subnet
          |                       |
      Load Balancer            Application
          |                       |
       Web Tier               Database
```

Terraform can manage:

- VPC
- Internet Gateway
- Public Subnets
- Private Subnets
- Route Tables
- NAT Gateway
- Security Groups
- Load Balancers
- EC2
- RDS
- IAM
- EKS
- CloudWatch

---

# Terraform Security Group Example

```hcl
resource "aws_security_group" "web" {
  name        = "web-sg"
  description = "Security group for web server"
  vpc_id      = aws_vpc.main.id

  ingress {
    description = "HTTP"
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    description = "HTTPS"
    from_port   = 443
    to_port     = 443
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```

---

# Terraform Lifecycle Rules

Terraform supports lifecycle configuration.

Example:

```hcl
resource "aws_instance" "web" {
  ami           = "ami-xxxxxxxxxxxxxxxxx"
  instance_type = "t2.micro"

  lifecycle {
    create_before_destroy = true
  }
}
```

Other lifecycle options include:

```hcl
lifecycle {
  prevent_destroy = true
}
```

and:

```hcl
lifecycle {
  ignore_changes = [
    tags
  ]
}
```

---

# Terraform Commands Cheat Sheet

## Initialization

```bash
terraform init
```

## Format

```bash
terraform fmt
```

## Recursive Format

```bash
terraform fmt -recursive
```

## Validate

```bash
terraform validate
```

## Plan

```bash
terraform plan
```

## Save Plan

```bash
terraform plan -out=tfplan
```

## Apply

```bash
terraform apply
```

## Auto Approve

```bash
terraform apply -auto-approve
```

## Destroy

```bash
terraform destroy
```

## Show State

```bash
terraform show
```

## List State Resources

```bash
terraform state list
```

## Show Resource

```bash
terraform state show aws_instance.web
```

## Remove Resource From State

```bash
terraform state rm aws_instance.web
```

## Import Existing Resource

```bash
terraform import aws_instance.web i-xxxxxxxxxxxxxxxxx
```

## Show Outputs

```bash
terraform output
```

## Show Specific Output

```bash
terraform output public_ip
```

## List Providers

```bash
terraform providers
```

## Generate Dependency Graph

```bash
terraform graph
```

## List Workspaces

```bash
terraform workspace list
```

## Create Workspace

```bash
terraform workspace new dev
```

## Select Workspace

```bash
terraform workspace select dev
```

## Current Workspace

```bash
terraform workspace show
```

## Delete Workspace

```bash
terraform workspace delete dev
```

## Replace Resource

```bash
terraform apply -replace="aws_instance.web"
```

## Target Resource

```bash
terraform plan -target="aws_instance.web"
```

## Upgrade Providers

```bash
terraform init -upgrade
```

## Reconfigure Backend

```bash
terraform init -reconfigure
```

## Migrate Backend

```bash
terraform init -migrate-state
```

---

# Terraform CI/CD Workflow

Terraform can be integrated with Jenkins or GitHub Actions.

Typical workflow:

```text
Developer
    |
    v
Git Push
    |
    v
GitHub
    |
    v
CI/CD Pipeline
    |
    +---- terraform fmt
    |
    +---- terraform validate
    |
    +---- terraform plan
    |
    v
Approval
    |
    v
terraform apply
    |
    v
AWS Infrastructure
```

---

# Example Jenkins Pipeline

```groovy
pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Terraform Init') {
            steps {
                sh 'terraform init'
            }
        }

        stage('Terraform Format') {
            steps {
                sh 'terraform fmt -check'
            }
        }

        stage('Terraform Validate') {
            steps {
                sh 'terraform validate'
            }
        }

        stage('Terraform Plan') {
            steps {
                sh 'terraform plan -out=tfplan'
            }
        }

        stage('Terraform Apply') {
            steps {
                input message: 'Approve Terraform Apply?'
                sh 'terraform apply tfplan'
            }
        }
    }
}
```

---

# Recommended Terraform Workflow

For every infrastructure change, follow:

```bash
git checkout -b feature/terraform-change

terraform fmt

terraform init

terraform validate

terraform plan -out=tfplan

terraform show tfplan

terraform apply tfplan

terraform output

git add .

git commit -m "Update Terraform infrastructure"

git push origin feature/terraform-change
```

Create a Pull Request and review the Terraform changes before merging.

---

# Production Best Practices

## 1. Use Remote State

Store state remotely rather than keeping it only on a developer laptop.

## 2. Use Version Control

Keep Terraform code in Git.

## 3. Use Modules

Create reusable modules for common infrastructure.

## 4. Avoid Hardcoded Secrets

Never put AWS access keys, passwords or tokens directly into Terraform files.

## 5. Use IAM Least Privilege

Give Terraform only the permissions it needs.

## 6. Review Terraform Plan

Always review:

```bash
terraform plan
```

before applying infrastructure changes.

## 7. Use CI/CD

Automate:

```text
fmt
validate
plan
approval
apply
```

## 8. Protect Production

Use approval gates before production deployment.

## 9. Pin Provider Versions

Use a controlled provider version:

```hcl
version = "~> 5.0"
```

## 10. Protect Terraform State

Terraform state may contain sensitive infrastructure information. Restrict access and secure the backend.

---

# Common Terraform Interview Questions

## What is Terraform?

Terraform is an Infrastructure as Code tool used to provision and manage infrastructure using declarative configuration files.

## What is Terraform State?

Terraform state stores information about resources managed by Terraform and helps Terraform compare configuration with real infrastructure.

## What is terraform plan?

`terraform plan` shows the changes Terraform intends to make without applying them.

## What is terraform apply?

`terraform apply` executes the proposed infrastructure changes.

## What is terraform destroy?

`terraform destroy` removes resources managed by Terraform.

## What is terraform init?

`terraform init` initializes the Terraform working directory and downloads required providers.

## What is a Terraform module?

A module is a reusable collection of Terraform configuration files.

## Why use remote state?

Remote state allows teams and CI/CD systems to share Terraform state safely.

## Difference between count and for_each?

`count` creates resources based on a numeric count, while `for_each` creates resources based on a map or set and is often better when resources have meaningful keys.

## What happens if Terraform state is deleted?

Terraform loses its record of managed resources. The real infrastructure may still exist, but Terraform may no longer know how to manage it correctly.

---

# My DevOps Learning

Terraform is an important part of modern DevOps because infrastructure can be managed in the same way as application code.

My current focus is:

```text
AWS
 |
 +-- VPC
 +-- EC2
 +-- IAM
 +-- Security Groups
 +-- Load Balancer
 +-- RDS
 +-- EKS
 |
Terraform
 |
Git
 |
Jenkins / GitHub Actions
 |
CI/CD
```

The goal is to automate infrastructure provisioning and integrate Terraform with CI/CD pipelines for reliable and repeatable deployments.

---

# Final Terraform Workflow

```bash
terraform init

terraform fmt -recursive

terraform validate

terraform plan -out=tfplan

terraform show tfplan

terraform apply tfplan

terraform output

terraform state list
```

When infrastructure is no longer required:

```bash
terraform destroy
```

---

# Conclusion

Terraform makes AWS infrastructure management more automated, consistent and repeatable.

Instead of manually creating infrastructure from the AWS Console, we can define our infrastructure as code, store it in Git, review changes through Pull Requests and deploy it through CI/CD.

A strong DevOps workflow can combine:

```text
AWS
+
Terraform
+
Git/GitHub
+
Jenkins/GitHub Actions
+
Docker
+
Kubernetes/EKS
+
Helm
+
Argo CD
+
Prometheus
+
Grafana
```

This creates an end-to-end DevOps environment where both infrastructure and applications can be automated.

---

# Tags

#Terraform #AWS #DevOps #InfrastructureAsCode #IaC #Cloud #AWSDevOps #Jenkins #GitHubActions #CI_CD #Kubernetes #EKS #Docker #Automation #DevOpsEngineer
