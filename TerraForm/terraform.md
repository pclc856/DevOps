# Terraform Complete Guide for DevOps Interview

## 1. Terraform Fundamentals

### What is Terraform?
- **Infrastructure as Code (IaC)** tool by HashiCorp
- **Declarative** approach to infrastructure management
- **Cloud-agnostic** - works with AWS, Azure, GCP, and more
- Creates, modifies, and versions infrastructure safely and efficiently

### Why Terraform?
- **Version Control** - Track infrastructure changes
- **Automation** - Reduce manual errors
- **Reproducibility** - Consistent environments
- **Collaboration** - Team-based infrastructure management
- **Multi-Cloud** - Single tool for multiple providers
- **State Management** - Tracks real-world resources

### Terraform vs Other IaC Tools

| Feature | Terraform | CloudFormation | Ansible |
|---------|-----------|----------------|---------|
| Type | Declarative | Declarative | Procedural |
| Cloud Support | Multi-cloud | AWS only | Multi-cloud |
| State Management | Yes | Yes | No |
| Language | HCL | JSON/YAML | YAML |
| Agentless | Yes | Yes | Yes (SSH) |

### Terraform Workflow

```
┌─────────────┐
│   Write     │  1. Write configuration files (.tf)
│   Config    │
└──────┬──────┘
       │
┌──────▼──────┐
│  terraform  │  2. Initialize working directory
│    init     │     Download providers & modules
└──────┬──────┘
       │
┌──────▼──────┐
│  terraform  │  3. Preview changes
│    plan     │     Show execution plan
└──────┬──────┘
       │
┌──────▼──────┐
│  terraform  │  4. Apply changes
│   apply     │     Create/modify resources
└──────┬──────┘
       │
┌──────▼──────┐
│  terraform  │  5. Destroy (if needed)
│  destroy    │     Remove all resources
└─────────────┘
```

---

## 2. Terraform Installation & Setup

### Installation

```bash
# Linux (Ubuntu/Debian)
wget https://releases.hashicorp.com/terraform/1.6.0/terraform_1.6.0_linux_amd64.zip
unzip terraform_1.6.0_linux_amd64.zip
sudo mv terraform /usr/local/bin/
terraform version

# macOS (Homebrew)
brew tap hashicorp/tap
brew install hashicorp/tap/terraform

# Windows (Chocolatey)
choco install terraform

# Verify installation
terraform version
terraform -help
```

### Basic Terraform Commands

```bash
# Initialize directory
terraform init

# Validate configuration
terraform validate

# Format code
terraform fmt

# Show execution plan
terraform plan
terraform plan -out=tfplan

# Apply changes
terraform apply
terraform apply tfplan
terraform apply -auto-approve

# Show current state
terraform show
terraform state list

# Destroy infrastructure
terraform destroy
terraform destroy -auto-approve

# Refresh state
terraform refresh

# Import existing resource
terraform import aws_instance.example i-1234567890abcdef0

# Output values
terraform output
terraform output -json

# Workspace management
terraform workspace list
terraform workspace new dev
terraform workspace select dev

# State management
terraform state list
terraform state show aws_instance.example
terraform state mv aws_instance.old aws_instance.new
terraform state rm aws_instance.example
```

---

## 3. Terraform Configuration Basics

### File Structure

```
project/
├── main.tf           # Main configuration
├── variables.tf      # Input variables
├── outputs.tf        # Output values
├── providers.tf      # Provider configuration
├── versions.tf       # Terraform & provider versions
├── terraform.tfvars  # Variable values (don't commit secrets!)
├── .terraform/       # Downloaded providers & modules
├── terraform.tfstate # State file (don't commit!)
└── .gitignore       # Ignore state and secrets
```

### HCL Syntax Basics

```hcl
# Comments
# Single line comment
// Also single line
/* Multi-line
   comment */

# Block structure
resource "aws_instance" "example" {
  ami           = "ami-12345678"
  instance_type = "t2.micro"
}

# Resource syntax
resource "<PROVIDER>_<TYPE>" "<NAME>" {
  <ARGUMENT> = <VALUE>
}

# Variables
variable "region" {
  type        = string
  default     = "us-east-1"
  description = "AWS region"
}

# Reference variable
region = var.region

# Locals
locals {
  common_tags = {
    Environment = "dev"
    ManagedBy   = "Terraform"
  }
}

# Reference local
tags = local.common_tags

# Outputs
output "instance_ip" {
  value = aws_instance.example.public_ip
}

# Data sources
data "aws_ami" "ubuntu" {
  most_recent = true
  owners      = ["099720109477"]
}

# Expressions
count    = 3
for_each = toset(["dev", "staging", "prod"])
```

---

## 4. Terraform Configuration Files

### main.tf - Basic AWS EC2 Example

```hcl
# Configure AWS Provider
terraform {
  required_version = ">= 1.0"
  
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = var.aws_region
  
  default_tags {
    tags = {
      Environment = var.environment
      ManagedBy   = "Terraform"
    }
  }
}

# VPC
resource "aws_vpc" "main" {
  cidr_block           = "10.0.0.0/16"
  enable_dns_hostnames = true
  enable_dns_support   = true

  tags = {
    Name = "${var.project_name}-vpc"
  }
}

# Internet Gateway
resource "aws_internet_gateway" "main" {
  vpc_id = aws_vpc.main.id

  tags = {
    Name = "${var.project_name}-igw"
  }
}

# Public Subnet
resource "aws_subnet" "public" {
  vpc_id                  = aws_vpc.main.id
  cidr_block              = "10.0.1.0/24"
  availability_zone       = "${var.aws_region}a"
  map_public_ip_on_launch = true

  tags = {
    Name = "${var.project_name}-public-subnet"
  }
}

# Route Table
resource "aws_route_table" "public" {
  vpc_id = aws_vpc.main.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.main.id
  }

  tags = {
    Name = "${var.project_name}-public-rt"
  }
}

# Route Table Association
resource "aws_route_table_association" "public" {
  subnet_id      = aws_subnet.public.id
  route_table_id = aws_route_table.public.id
}

# Security Group
resource "aws_security_group" "web" {
  name        = "${var.project_name}-web-sg"
  description = "Security group for web servers"
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

  ingress {
    description = "SSH"
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = var.ssh_allowed_ips
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = {
    Name = "${var.project_name}-web-sg"
  }
}

# EC2 Instance
resource "aws_instance" "web" {
  ami                    = data.aws_ami.ubuntu.id
  instance_type          = var.instance_type
  subnet_id              = aws_subnet.public.id
  vpc_security_group_ids = [aws_security_group.web.id]
  key_name               = var.key_name

  user_data = <<-EOF
              #!/bin/bash
              apt-get update
              apt-get install -y nginx
              systemctl start nginx
              systemctl enable nginx
              echo "<h1>Hello from Terraform!</h1>" > /var/www/html/index.html
              EOF

  root_block_device {
    volume_size = 20
    volume_type = "gp3"
    encrypted   = true
  }

  tags = {
    Name = "${var.project_name}-web-server"
  }
}

# Data source for latest Ubuntu AMI
data "aws_ami" "ubuntu" {
  most_recent = true
  owners      = ["099720109477"] # Canonical

  filter {
    name   = "name"
    values = ["ubuntu/images/hvm-ssd/ubuntu-22.04-amd64-server-*"]
  }

  filter {
    name   = "virtualization-type"
    values = ["hvm"]
  }
}
```

### variables.tf

```hcl
variable "aws_region" {
  description = "AWS region for resources"
  type        = string
  default     = "us-east-1"
}

variable "project_name" {
  description = "Project name for resource naming"
  type        = string
  default     = "myapp"
}

variable "environment" {
  description = "Environment (dev, staging, prod)"
  type        = string
  default     = "dev"
  
  validation {
    condition     = contains(["dev", "staging", "prod"], var.environment)
    error_message = "Environment must be dev, staging, or prod."
  }
}

variable "instance_type" {
  description = "EC2 instance type"
  type        = string
  default     = "t2.micro"
}

variable "key_name" {
  description = "SSH key name"
  type        = string
}

variable "ssh_allowed_ips" {
  description = "IP addresses allowed to SSH"
  type        = list(string)
  default     = ["0.0.0.0/0"]
}

# Variable types examples
variable "string_example" {
  type    = string
  default = "hello"
}

variable "number_example" {
  type    = number
  default = 42
}

variable "bool_example" {
  type    = bool
  default = true
}

variable "list_example" {
  type    = list(string)
  default = ["item1", "item2", "item3"]
}

variable "map_example" {
  type = map(string)
  default = {
    key1 = "value1"
    key2 = "value2"
  }
}

variable "object_example" {
  type = object({
    name = string
    age  = number
  })
  default = {
    name = "John"
    age  = 30
  }
}

# Sensitive variable
variable "db_password" {
  description = "Database password"
  type        = string
  sensitive   = true
}
```

### outputs.tf

```hcl
output "vpc_id" {
  description = "VPC ID"
  value       = aws_vpc.main.id
}

output "public_subnet_id" {
  description = "Public subnet ID"
  value       = aws_subnet.public.id
}

output "instance_id" {
  description = "EC2 instance ID"
  value       = aws_instance.web.id
}

output "instance_public_ip" {
  description = "Public IP of EC2 instance"
  value       = aws_instance.web.public_ip
}

output "instance_public_dns" {
  description = "Public DNS of EC2 instance"
  value       = aws_instance.web.public_dns
}

output "security_group_id" {
  description = "Security group ID"
  value       = aws_security_group.web.id
}

# Sensitive output
output "db_password" {
  description = "Database password"
  value       = var.db_password
  sensitive   = true
}

# Complex output
output "instance_details" {
  description = "Complete instance details"
  value = {
    id         = aws_instance.web.id
    public_ip  = aws_instance.web.public_ip
    private_ip = aws_instance.web.private_ip
    type       = aws_instance.web.instance_type
  }
}
```

### terraform.tfvars

```hcl
# Variable values (DO NOT commit secrets!)
aws_region     = "us-west-2"
project_name   = "myproject"
environment    = "prod"
instance_type  = "t3.medium"
key_name       = "my-ssh-key"
ssh_allowed_ips = ["203.0.113.0/24"]

# Use environment variables for secrets
# export TF_VAR_db_password="secret123"
```

### providers.tf

```hcl
terraform {
  required_version = ">= 1.0"
  
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
    random = {
      source  = "hashicorp/random"
      version = "~> 3.0"
    }
  }
  
  # Backend configuration for remote state
  backend "s3" {
    bucket         = "my-terraform-state"
    key            = "prod/terraform.tfstate"
    region         = "us-east-1"
    encrypt        = true
    dynamodb_table = "terraform-locks"
  }
}

provider "aws" {
  region = var.aws_region
  
  # Assume role (for cross-account access)
  assume_role {
    role_arn = "arn:aws:iam::123456789012:role/TerraformRole"
  }
}

# Multiple provider configurations
provider "aws" {
  alias  = "west"
  region = "us-west-2"
}

# Use aliased provider
resource "aws_instance" "west_server" {
  provider = aws.west
  ami      = "ami-12345678"
  instance_type = "t2.micro"
}
```

---

## 5. Terraform State Management

### What is Terraform State?
- **Mapping** between configuration and real-world resources
- **Metadata** about resources
- **Performance** cache for resource attributes
- **Dependency tracking**

### Local State (Default)

```hcl
# terraform.tfstate stored locally
# Simple but not recommended for teams
```

### Remote State (Recommended)

```hcl
# S3 Backend (AWS)
terraform {
  backend "s3" {
    bucket         = "my-terraform-state"
    key            = "prod/terraform.tfstate"
    region         = "us-east-1"
    encrypt        = true
    dynamodb_table = "terraform-state-locks"
    kms_key_id     = "arn:aws:kms:us-east-1:123456789012:key/12345678"
  }
}

# Azure Backend
terraform {
  backend "azurerm" {
    resource_group_name  = "terraform-rg"
    storage_account_name = "tfstate"
    container_name       = "tfstate"
    key                  = "prod.terraform.tfstate"
  }
}

# Google Cloud Backend
terraform {
  backend "gcs" {
    bucket = "tf-state-bucket"
    prefix = "terraform/state"
  }
}

# Terraform Cloud Backend
terraform {
  backend "remote" {
    organization = "my-org"
    workspaces {
      name = "my-workspace"
    }
  }
}
```

### State Commands

```bash
# List resources in state
terraform state list

# Show resource details
terraform state show aws_instance.web

# Move resource in state
terraform state mv aws_instance.old aws_instance.new

# Remove resource from state (doesn't destroy)
terraform state rm aws_instance.web

# Pull remote state
terraform state pull > terraform.tfstate

# Push local state to remote
terraform state push

# Replace provider in state
terraform state replace-provider hashicorp/aws registry.terraform.io/hashicorp/aws

# Import existing resource
terraform import aws_instance.web i-1234567890abcdef0
```

### State Locking

```hcl
# DynamoDB table for state locking (AWS)
resource "aws_dynamodb_table" "terraform_locks" {
  name         = "terraform-state-locks"
  billing_mode = "PAY_PER_REQUEST"
  hash_key     = "LockID"

  attribute {
    name = "LockID"
    type = "S"
  }

  tags = {
    Name = "Terraform State Locks"
  }
}
```

---

## 6. Terraform Modules

### What are Modules?
- **Reusable** Terraform configurations
- **Encapsulation** of resources
- **Abstraction** layer
- **Organization** and structure

### Module Structure

```
modules/
└── vpc/
    ├── main.tf
    ├── variables.tf
    ├── outputs.tf
    └── README.md
```

### Creating a Module (modules/vpc/main.tf)

```hcl
# modules/vpc/main.tf
resource "aws_vpc" "this" {
  cidr_block           = var.vpc_cidr
  enable_dns_hostnames = var.enable_dns_hostnames
  enable_dns_support   = var.enable_dns_support

  tags = merge(
    var.tags,
    {
      Name = var.vpc_name
    }
  )
}

resource "aws_subnet" "public" {
  count                   = length(var.public_subnet_cidrs)
  vpc_id                  = aws_vpc.this.id
  cidr_block              = var.public_subnet_cidrs[count.index]
  availability_zone       = var.availability_zones[count.index]
  map_public_ip_on_launch = true

  tags = merge(
    var.tags,
    {
      Name = "${var.vpc_name}-public-${count.index + 1}"
      Type = "public"
    }
  )
}

resource "aws_internet_gateway" "this" {
  vpc_id = aws_vpc.this.id

  tags = merge(
    var.tags,
    {
      Name = "${var.vpc_name}-igw"
    }
  )
}
```

### Module Variables (modules/vpc/variables.tf)

```hcl
variable "vpc_name" {
  description = "Name of the VPC"
  type        = string
}

variable "vpc_cidr" {
  description = "CIDR block for VPC"
  type        = string
  default     = "10.0.0.0/16"
}

variable "public_subnet_cidrs" {
  description = "CIDR blocks for public subnets"
  type        = list(string)
}

variable "availability_zones" {
  description = "Availability zones"
  type        = list(string)
}

variable "enable_dns_hostnames" {
  description = "Enable DNS hostnames"
  type        = bool
  default     = true
}

variable "enable_dns_support" {
  description = "Enable DNS support"
  type        = bool
  default     = true
}

variable "tags" {
  description = "Tags to apply to resources"
  type        = map(string)
  default     = {}
}
```

### Module Outputs (modules/vpc/outputs.tf)

```hcl
output "vpc_id" {
  description = "VPC ID"
  value       = aws_vpc.this.id
}

output "vpc_cidr" {
  description = "VPC CIDR block"
  value       = aws_vpc.this.cidr_block
}

output "public_subnet_ids" {
  description = "List of public subnet IDs"
  value       = aws_subnet.public[*].id
}

output "internet_gateway_id" {
  description = "Internet Gateway ID"
  value       = aws_internet_gateway.this.id
}
```

### Using a Module

```hcl
# main.tf
module "vpc" {
  source = "./modules/vpc"

  vpc_name             = "my-vpc"
  vpc_cidr             = "10.0.0.0/16"
  public_subnet_cidrs  = ["10.0.1.0/24", "10.0.2.0/24"]
  availability_zones   = ["us-east-1a", "us-east-1b"]
  
  tags = {
    Environment = "production"
    ManagedBy   = "Terraform"
  }
}

# Reference module outputs
resource "aws_instance" "web" {
  ami           = "ami-12345678"
  instance_type = "t2.micro"
  subnet_id     = module.vpc.public_subnet_ids[0]
}

output "vpc_id" {
  value = module.vpc.vpc_id
}
```

### Using Public Modules

```hcl
# From Terraform Registry
module "vpc" {
  source  = "terraform-aws-modules/vpc/aws"
  version = "5.0.0"

  name = "my-vpc"
  cidr = "10.0.0.0/16"

  azs             = ["us-east-1a", "us-east-1b"]
  private_subnets = ["10.0.1.0/24", "10.0.2.0/24"]
  public_subnets  = ["10.0.101.0/24", "10.0.102.0/24"]

  enable_nat_gateway = true
  enable_vpn_gateway = false

  tags = {
    Terraform   = "true"
    Environment = "dev"
  }
}

# From GitHub
module "consul" {
  source = "github.com/hashicorp/consul/terraform-aws-consul"
}

# From Git with specific branch/tag
module "consul" {
  source = "git::https://github.com/hashicorp/terraform-aws-consul.git?ref=v0.1.0"
}
```

---

## 7. Advanced Terraform Concepts

### Count Meta-Argument

```hcl
# Create multiple similar resources
resource "aws_instance" "server" {
  count = 3

  ami           = "ami-12345678"
  instance_type = "t2.micro"

  tags = {
    Name = "Server-${count.index + 1}"
  }
}

# Access resources
output "server_ids" {
  value = aws_instance.server[*].id
}

# Conditional resource creation
resource "aws_instance" "example" {
  count = var.create_instance ? 1 : 0

  ami           = "ami-12345678"
  instance_type = "t2.micro"
}
```

### for_each Meta-Argument

```hcl
# With set
resource "aws_iam_user" "users" {
  for_each = toset(["user1", "user2", "user3"])
  
  name = each.key
}

# With map
variable "instances" {
  type = map(object({
    instance_type = string
    ami           = string
  }))
  
  default = {
    web = {
      instance_type = "t2.micro"
      ami           = "ami-12345678"
    }
    app = {
      instance_type = "t2.small"
      ami           = "ami-87654321"
    }
  }
}

resource "aws_instance" "servers" {
  for_each = var.instances

  ami           = each.value.ami
  instance_type = each.value.instance_type

  tags = {
    Name = each.key
  }
}

# Access resources
output "instance_ips" {
  value = {
    for k, instance in aws_instance.servers :
    k => instance.public_ip
  }
}
```

### Dynamic Blocks

```hcl
variable "ingress_rules" {
  type = list(object({
    port        = number
    protocol    = string
    cidr_blocks = list(string)
    description = string
  }))
  
  default = [
    {
      port        = 80
      protocol    = "tcp"
      cidr_blocks = ["0.0.0.0/0"]
      description = "HTTP"
    },
    {
      port        = 443
      protocol    = "tcp"
      cidr_blocks = ["0.0.0.0/0"]
      description = "HTTPS"
    }
  ]
}

resource "aws_security_group" "web" {
  name        = "web-sg"
  description = "Web security group"
  vpc_id      = aws_vpc.main.id

  dynamic "ingress" {
    for_each = var.ingress_rules
    content {
      description = ingress.value.description
      from_port   = ingress.value.port
      to_port     = ingress.value.port
      protocol    = ingress.value.protocol
      cidr_blocks = ingress.value.cidr_blocks
    }
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```

### Conditional Expressions

```hcl
# Ternary operator
resource "aws_instance" "example" {
  ami           = "ami-12345678"
  instance_type = var.environment == "prod" ? "t3.large" : "t2.micro"
}

# Conditional resource
resource "aws_eip" "example" {
  count = var.environment == "prod" ? 1 : 0

  instance = aws_instance.web.id
}

# Conditional tags
resource "aws_instance" "example" {
  ami           = "ami-12345678"
  instance_type = "t2.micro"

  tags = var.environment == "prod" ? {
    Name        = "prod-server"
    Backup      = "true"
    Monitoring  = "enabled"
  } : {
    Name = "dev-server"
  }
}
```

### Locals

```hcl
locals {
  # Common tags
  common_tags = {
    Project     = var.project_name
    Environment = var.environment
    ManagedBy   = "Terraform"
    Owner       = "DevOps Team"
  }

  # Computed values
  instance_name = "${var.project_name}-${var.environment}-web"
  
  # Conditional logic
  enable_monitoring = var.environment == "prod" ? true : false
  
  # Lists and maps
  availability_zones = ["us-east-1a", "us-east-1b", "us-east-1c"]
  
  # Complex expressions
  subnet_cidrs = [
    for i in range(3) : cidrsubnet(var.vpc_cidr, 8, i)
  ]
}

resource "aws_instance" "web" {
  ami           = "ami-12345678"
  instance_type = "t2.micro"
  
  tags = merge(
    local.common_tags,
    {
      Name = local.instance_name
    }
  )
  
  monitoring = local.enable_monitoring
}
```

### Data Sources

```hcl
# Get latest AMI
data "aws_ami" "ubuntu" {
  most_recent = true
  owners      = ["099720109477"] # Canonical

  filter {
    name   = "name"
    values = ["ubuntu/images/hvm-ssd/ubuntu-22.04-amd64-server-*"]
  }

  filter {
    name   = "virtualization-type"
    values = ["hvm"]
  }
}

# Get current region
data "aws_region" "current" {}

# Get account ID
data "aws_caller_identity" "current" {}

# Get availability zones
data "aws_availability_zones" "available" {
  state = "available"
}

# Use data sources
resource "aws_instance" "web" {
  ami           = data.aws_ami.ubuntu.id
  instance_type = "t2.micro"
  
  tags = {
    Name      = "web-server"
    Region    = data.aws_region.current.name
    AccountID = data.aws_caller_identity.current.account_id
  }
}

output "available_azs" {
  value = data.aws_availability_zones.available.names
}
```

### Provisioners

```hcl
# File provisioner
resource "aws_instance" "web" {
  ami           = "ami-12345678"
  instance_type = "t2.micro"
  key_name      = var.key_name

  provisioner "file" {
    source      = "app.conf"
    destination = "/etc/app.conf"

    connection {
      type        = "ssh"
      user        = "ubuntu"
      private_key = file(var.private_key_path)
      host        = self.public_ip
    }
  }

  # Remote-exec provisioner
  provisioner "remote-exec" {
    inline = [
      "sudo apt-get update",
      "sudo apt-get install -y nginx",
      "sudo systemctl start nginx",
    ]

    connection {
      type        = "ssh"
      user        = "ubuntu"
      private_key = file(var.private_key_path)
      host        = self.public_ip
    }
  }

  # Local-exec provisioner
  provisioner "local-exec" {
    command = "echo ${self.public_ip} >> ips.txt"
  }

  # Destroy-time provisioner
  provisioner "local-exec" {
    when    = destroy
    command = "echo 'Instance destroyed' >> log.txt"
  }
}
```

### Functions

```hcl
# String functions
upper("hello")                    # "HELLO"
lower("HELLO")                    # "hello"
title("hello world")              # "Hello World"
trim("  hello  ", " ")            # "hello"
replace("hello-world", "-", "_") # "hello_world"
split("-", "a-b-c")              # ["a", "b", "c"]
join("-", ["a", "b", "c"])       # "a-b-c"
format("Hello, %s!", "World")     # "Hello, World!"

# Numeric functions
abs(-5)                  # 5
ceil(5.1)               # 6
floor(5.9)              # 5
max(5, 12, 9)           # 12
min(5, 12, 9)           # 5

# Collection functions
length([1, 2, 3])                    # 3
concat([1, 2], [3, 4])              # [1, 2, 3, 4]
contains(["a", "b"], "a")           # true
element(["a", "b", "c"], 1)         # "b"
index(["a", "b", "c"], "b")         # 1
lookup({a=1, b=2}, "a", 0)          # 1
merge({a=1}, {b=2})                 # {a=1, b=2}
keys({a=1, b=2})                    # ["a", "b"]
values({a=1, b=2})                  # [1, 2]

# Type conversion
tostring(42)                         # "42"
tonumber("42")                       # 42
tobool("true")                       # true
tolist(["a", "b"])                  # ["a", "b"]
toset(["a", "b", "a"])              # ["a", "b"]
tomap({a=1, b=2})                   # {a=1, b=2}

# IP Network functions
cidrhost("10.0.0.0/16", 5)          # "10.0.0.5"
cidrnetmask("10.0.0.0/16")          # "255.255.0.0"
cidrsubnet("10.0.0.0/16", 8, 2)     # "10.0.2.0/24"

# Filesystem functions
file("path/to/file")                 # Read file contents
fileexists("path/to/file")          # true/false
basename("path/to/file.txt")        # "file.txt"
dirname("path/to/file.txt")         # "path/to"

# Date and time
timestamp()                          # "2024-01-15T10:30:00Z"
formatdate("YYYY-MM-DD", timestamp()) # "2024-01-15"

# Encoding functions
base64encode("hello")                # "aGVsbG8="
base64decode("aGVsbG8=")            # "hello"
jsonencode({a=1, b=2})              # "{\"a\":1,\"b\":2}"
jsondecode("{\"a\":1}")             # {a=1}

# Example usage
locals {
  subnet_cidrs = [
    for i in range(3) : cidrsubnet(var.vpc_cidr, 8, i)
  ]
  
  uppercase_tags = {
    for k, v in var.tags : k => upper(v)
  }
  
  availability_zones = slice(
    data.aws_availability_zones.available.names,
    0,
    min(length(data.aws_availability_zones.available.names), 3)
  )
}
```

---

## 8. Terraform Workspaces

### What are Workspaces?
- Manage **multiple environments** with same configuration
- Separate state files per workspace
- Alternative to separate directories

### Workspace Commands

```bash
# List workspaces
terraform workspace list

# Create new workspace
terraform workspace new dev
terraform workspace new staging
terraform workspace new prod

# Switch workspace
terraform workspace select dev

# Show current workspace
terraform workspace show

# Delete workspace
terraform workspace delete dev
```

### Using Workspaces in Configuration

```hcl
# Reference current workspace
resource "aws_instance" "web" {
  ami           = "ami-12345678"
  instance_type = terraform.workspace == "prod" ? "t3.large" : "t2.micro"
  
  tags = {
    Name        = "${terraform.workspace}-web-server"
    Environment = terraform.workspace
  }
}

# Workspace-specific variables
locals {
  env_config = {
    dev = {
      instance_type = "t2.micro"
      instance_count = 1
    }
    staging = {
      instance_type = "t2.small"
      instance_count = 2
    }
    prod = {
      instance_type = "t3.large"
      instance_count = 5
    }
  }
  
  current_config = local.env_config[terraform.workspace]
}

resource "aws_instance" "app" {
  count         = local.current_config.instance_count
  ami           = "ami-12345678"
  instance_type = local.current_config.instance_type
  
  tags = {
    Name = "${terraform.workspace}-app-${count.index + 1}"
  }
}
```

---

## 9. Real-World Terraform Examples

### Complete 3-Tier Web Application (AWS)

```hcl
# variables.tf
variable "aws_region" {
  default = "us-east-1"
}

variable "environment" {
  default = "production"
}

variable "project_name" {
  default = "webapp"
}

variable "db_password" {
  type      = string
  sensitive = true
}

# main.tf
terraform {
  required_version = ">= 1.0"
  
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
  
  backend "s3" {
    bucket         = "my-terraform-state"
    key            = "webapp/terraform.tfstate"
    region         = "us-east-1"
    encrypt        = true
    dynamodb_table = "terraform-locks"
  }
}

provider "aws" {
  region = var.aws_region
}

# VPC Module
module "vpc" {
  source = "terraform-aws-modules/vpc/aws"
  version = "5.0.0"

  name = "${var.project_name}-${var.environment}-vpc"
  cidr = "10.0.0.0/16"

  azs             = ["${var.aws_region}a", "${var.aws_region}b", "${var.aws_region}c"]
  private_subnets = ["10.0.1.0/24", "10.0.2.0/24", "10.0.3.0/24"]
  public_subnets  = ["10.0.101.0/24", "10.0.102.0/24", "10.0.103.0/24"]
  database_subnets = ["10.0.201.0/24", "10.0.202.0/24", "10.0.203.0/24"]

  enable_nat_gateway = true
  enable_vpn_gateway = false
  enable_dns_hostnames = true
  enable_dns_support   = true

  tags = local.common_tags
}

# Application Load Balancer
resource "aws_lb" "app" {
  name               = "${var.project_name}-${var.environment}-alb"
  internal           = false
  load_balancer_type = "application"
  security_groups    = [aws_security_group.alb.id]
  subnets            = module.vpc.public_subnets

  enable_deletion_protection = var.environment == "production" ? true : false

  tags = local.common_tags
}

resource "aws_lb_target_group" "app" {
  name     = "${var.project_name}-${var.environment}-tg"
  port     = 80
  protocol = "HTTP"
  vpc_id   = module.vpc.vpc_id

  health_check {
    enabled             = true
    healthy_threshold   = 2
    interval            = 30
    matcher             = "200"
    path                = "/health"
    port                = "traffic-port"
    protocol            = "HTTP"
    timeout             = 5
    unhealthy_threshold = 2
  }

  tags = local.common_tags
}

resource "aws_lb_listener" "http" {
  load_balancer_arn = aws_lb.app.arn
  port              = "80"
  protocol          = "HTTP"

  default_action {
    type             = "forward"
    target_group_arn = aws_lb_target_group.app.arn
  }
}

# Launch Template for Auto Scaling
resource "aws_launch_template" "app" {
  name_prefix   = "${var.project_name}-${var.environment}-"
  image_id      = data.aws_ami.amazon_linux.id
  instance_type = var.environment == "production" ? "t3.medium" : "t3.micro"

  vpc_security_group_ids = [aws_security_group.app.id]

  user_data = base64encode(templatefile("${path.module}/user-data.sh", {
    db_endpoint = aws_db_instance.main.endpoint
    db_name     = aws_db_instance.main.db_name
  }))

  iam_instance_profile {
    name = aws_iam_instance_profile.app.name
  }

  monitoring {
    enabled = true
  }

  tag_specifications {
    resource_type = "instance"
    tags = merge(
      local.common_tags,
      {
        Name = "${var.project_name}-${var.environment}-app"
      }
    )
  }
}

# Auto Scaling Group
resource "aws_autoscaling_group" "app" {
  name                = "${var.project_name}-${var.environment}-asg"
  vpc_zone_identifier = module.vpc.private_subnets
  target_group_arns   = [aws_lb_target_group.app.arn]
  health_check_type   = "ELB"
  health_check_grace_period = 300

  min_size         = 2
  max_size         = 10
  desired_capacity = var.environment == "production" ? 4 : 2

  launch_template {
    id      = aws_launch_template.app.id
    version = "$Latest"
  }

  tag {
    key                 = "Name"
    value               = "${var.project_name}-${var.environment}-app"
    propagate_at_launch = true
  }

  dynamic "tag" {
    for_each = local.common_tags
    content {
      key                 = tag.key
      value               = tag.value
      propagate_at_launch = true
    }
  }
}

# Auto Scaling Policies
resource "aws_autoscaling_policy" "scale_up" {
  name                   = "${var.project_name}-${var.environment}-scale-up"
  scaling_adjustment     = 2
  adjustment_type        = "ChangeInCapacity"
  cooldown               = 300
  autoscaling_group_name = aws_autoscaling_group.app.name
}

resource "aws_autoscaling_policy" "scale_down" {
  name                   = "${var.project_name}-${var.environment}-scale-down"
  scaling_adjustment     = -1
  adjustment_type        = "ChangeInCapacity"
  cooldown               = 300
  autoscaling_group_name = aws_autoscaling_group.app.name
}

# CloudWatch Alarms
resource "aws_cloudwatch_metric_alarm" "cpu_high" {
  alarm_name          = "${var.project_name}-${var.environment}-cpu-high"
  comparison_operator = "GreaterThanThreshold"
  evaluation_periods  = "2"
  metric_name         = "CPUUtilization"
  namespace           = "AWS/EC2"
  period              = "120"
  statistic           = "Average"
  threshold           = "70"

  dimensions = {
    AutoScalingGroupName = aws_autoscaling_group.app.name
  }

  alarm_actions = [aws_autoscaling_policy.scale_up.arn]
}

resource "aws_cloudwatch_metric_alarm" "cpu_low" {
  alarm_name          = "${var.project_name}-${var.environment}-cpu-low"
  comparison_operator = "LessThanThreshold"
  evaluation_periods  = "2"
  metric_name         = "CPUUtilization"
  namespace           = "AWS/EC2"
  period              = "120"
  statistic           = "Average"
  threshold           = "30"

  dimensions = {
    AutoScalingGroupName = aws_autoscaling_group.app.name
  }

  alarm_actions = [aws_autoscaling_policy.scale_down.arn]
}

# RDS Database
resource "aws_db_subnet_group" "main" {
  name       = "${var.project_name}-${var.environment}-db-subnet"
  subnet_ids = module.vpc.database_subnets

  tags = local.common_tags
}

resource "aws_db_instance" "main" {
  identifier     = "${var.project_name}-${var.environment}-db"
  engine         = "postgres"
  engine_version = "15.3"
  instance_class = var.environment == "production" ? "db.t3.medium" : "db.t3.micro"

  allocated_storage     = 20
  max_allocated_storage = 100
  storage_encrypted     = true

  db_name  = replace(var.project_name, "-", "")
  username = "admin"
  password = var.db_password

  vpc_security_group_ids = [aws_security_group.rds.id]
  db_subnet_group_name   = aws_db_subnet_group.main.name

  backup_retention_period = var.environment == "production" ? 7 : 1
  backup_window          = "03:00-04:00"
  maintenance_window     = "mon:04:00-mon:05:00"

  multi_az               = var.environment == "production" ? true : false
  skip_final_snapshot    = var.environment != "production"
  final_snapshot_identifier = var.environment == "production" ? "${var.project_name}-${var.environment}-final-snapshot" : null

  enabled_cloudwatch_logs_exports = ["postgresql", "upgrade"]

  tags = local.common_tags
}

# Security Groups
resource "aws_security_group" "alb" {
  name        = "${var.project_name}-${var.environment}-alb-sg"
  description = "Security group for ALB"
  vpc_id      = module.vpc.vpc_id

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
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

  tags = merge(
    local.common_tags,
    {
      Name = "${var.project_name}-${var.environment}-alb-sg"
    }
  )
}

resource "aws_security_group" "app" {
  name        = "${var.project_name}-${var.environment}-app-sg"
  description = "Security group for application servers"
  vpc_id      = module.vpc.vpc_id

  ingress {
    from_port       = 80
    to_port         = 80
    protocol        = "tcp"
    security_groups = [aws_security_group.alb.id]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = merge(
    local.common_tags,
    {
      Name = "${var.project_name}-${var.environment}-app-sg"
    }
  )
}

resource "aws_security_group" "rds" {
  name        = "${var.project_name}-${var.environment}-rds-sg"
  description = "Security group for RDS database"
  vpc_id      = module.vpc.vpc_id

  ingress {
    from_port       = 5432
    to_port         = 5432
    protocol        = "tcp"
    security_groups = [aws_security_group.app.id]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = merge(
    local.common_tags,
    {
      Name = "${var.project_name}-${var.environment}-rds-sg"
    }
  )
}

# IAM Role for EC2
resource "aws_iam_role" "app" {
  name = "${var.project_name}-${var.environment}-app-role"

  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Action = "sts:AssumeRole"
        Effect = "Allow"
        Principal = {
          Service = "ec2.amazonaws.com"
        }
      }
    ]
  })

  tags = local.common_tags
}

resource "aws_iam_role_policy_attachment" "app_ssm" {
  role       = aws_iam_role.app.name
  policy_arn = "arn:aws:iam::aws:policy/AmazonSSMManagedInstanceCore"
}

resource "aws_iam_role_policy_attachment" "app_cloudwatch" {
  role       = aws_iam_role.app.name
  policy_arn = "arn:aws:iam::aws:policy/CloudWatchAgentServerPolicy"
}

resource "aws_iam_instance_profile" "app" {
  name = "${var.project_name}-${var.environment}-app-profile"
  role = aws_iam_role.app.name
}

# Data Sources
data "aws_ami" "amazon_linux" {
  most_recent = true
  owners      = ["amazon"]

  filter {
    name   = "name"
    values = ["amzn2-ami-hvm-*-x86_64-gp2"]
  }
}

# Locals
locals {
  common_tags = {
    Project     = var.project_name
    Environment = var.environment
    ManagedBy   = "Terraform"
    Owner       = "DevOps Team"
  }
}

# Outputs
output "alb_dns_name" {
  description = "DNS name of the load balancer"
  value       = aws_lb.app.dns_name
}

output "rds_endpoint" {
  description = "RDS database endpoint"
  value       = aws_db_instance.main.endpoint
  sensitive   = true
}

output "vpc_id" {
  description = "VPC ID"
  value       = module.vpc.vpc_id
}
```

### Kubernetes Cluster on AWS (EKS)

```hcl
# eks-cluster.tf
module "eks" {
  source  = "terraform-aws-modules/eks/aws"
  version = "19.0.0"

  cluster_name    = "${var.project_name}-${var.environment}"
  cluster_version = "1.27"

  vpc_id     = module.vpc.vpc_id
  subnet_ids = module.vpc.private_subnets

  cluster_endpoint_public_access = true

  eks_managed_node_groups = {
    general = {
      min_size     = 2
      max_size     = 10
      desired_size = 3

      instance_types = ["t3.medium"]
      capacity_type  = "ON_DEMAND"

      labels = {
        role = "general"
      }

      tags = local.common_tags
    }
  }

  tags = local.common_tags
}

# IRSA for EBS CSI Driver
module "ebs_csi_irsa" {
  source  = "terraform-aws-modules/iam/aws//modules/iam-role-for-service-accounts-eks"
  version = "5.0.0"

  role_name = "${var.project_name}-ebs-csi"

  attach_ebs_csi_policy = true

  oidc_providers = {
    main = {
      provider_arn               = module.eks.oidc_provider_arn
      namespace_service_accounts = ["kube-system:ebs-csi-controller-sa"]
    }
  }
}

# EBS CSI Driver
resource "aws_eks_addon" "ebs_csi" {
  cluster_name = module.eks.cluster_name
  addon_name   = "aws-ebs-csi-driver"

  service_account_role_arn = module.ebs_csi_irsa.iam_role_arn

  depends_on = [module.eks]
}
```

### Multi-Region Deployment

```hcl
# providers.tf
provider "aws" {
  alias  = "us_east"
  region = "us-east-1"
}

provider "aws" {
  alias  = "us_west"
  region = "us-west-2"
}

# main.tf
module "infrastructure_us_east" {
  source = "./modules/infrastructure"
  
  providers = {
    aws = aws.us_east
  }

  region       = "us-east-1"
  project_name = var.project_name
  environment  = var.environment
}

module "infrastructure_us_west" {
  source = "./modules/infrastructure"
  
  providers = {
    aws = aws.us_west
  }

  region       = "us-west-2"
  project_name = var.project_name
  environment  = var.environment
}

# Route53 for multi-region
resource "aws_route53_zone" "main" {
  name = var.domain_name
}

resource "aws_route53_record" "us_east" {
  zone_id = aws_route53_zone.main.zone_id
  name    = var.domain_name
  type    = "A"

  weighted_routing_policy {
    weight = 50
  }

  set_identifier = "us-east-1"

  alias {
    name                   = module.infrastructure_us_east.alb_dns_name
    zone_id                = module.infrastructure_us_east.alb_zone_id
    evaluate_target_health = true
  }
}

resource "aws_route53_record" "us_west" {
  zone_id = aws_route53_zone.main.zone_id
  name    = var.domain_name
  type    = "A"

  weighted_routing_policy {
    weight = 50
  }

  set_identifier = "us-west-2"

  alias {
    name                   = module.infrastructure_us_west.alb_dns_name
    zone_id                = module.infrastructure_us_west.alb_zone_id
    evaluate_target_health = true
  }
}
```

---

## 10. Terraform Best Practices

### 1. Code Organization

```
project/
├── environments/
│   ├── dev/
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   └── terraform.tfvars
│   ├── staging/
│   └── prod/
├── modules/
│   ├── networking/
│   ├── compute/
│   └── database/
├── .gitignore
└── README.md
```

### 2. State Management
- Always use remote backend
- Enable state locking
- Encrypt state files
- Never commit state to version control

### 3. Variable Management
```hcl
# Use environment variables for secrets
export TF_VAR_db_password="secret123"

# Use .tfvars files for different environments
terraform apply -var-file="prod.tfvars"

# Mark sensitive variables
variable "db_password" {
  type      = string
  sensitive = true
}
```

### 4. Resource Naming
```hcl
# Consistent naming convention
resource "aws_instance" "web" {
  tags = {
    Name = "${var.project_name}-${var.environment}-web-${count.index + 1}"
  }
}
```

### 5. Use Modules
- Create reusable modules
- Version your modules
- Use public modules from registry

### 6. Documentation
```hcl
# Document variables
variable "instance_type" {
  description = "EC2 instance type for application servers"
  type        = string
  default     = "t3.micro"
}

# Document outputs
output "alb_dns" {
  description = "DNS name of the Application Load Balancer"
  value       = aws_lb.main.dns_name
}
```

### 7. Tagging Strategy
```hcl
locals {
  common_tags = {
    Project     = var.project_name
    Environment = var.environment
    ManagedBy   = "Terraform"
    Owner       = var.owner
    CostCenter  = var.cost_center
  }
}
```

### 8. Version Constraints
```hcl
terraform {
  required_version = ">= 1.0, < 2.0"
  
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"  # Allow 5.x versions
    }
  }
}
```

### 9. Use Data Sources
```hcl
# Instead of hardcoding AMI IDs
data "aws_ami" "latest" {
  most_recent = true
  owners      = ["amazon"]
  
  filter {
    name   = "name"
    values = ["amzn2-ami-hvm-*"]
  }
}
```

### 10. Validate and Format
```bash
# Always validate
terraform validate

# Format code consistently
terraform fmt -recursive

# Use pre-commit hooks
```

---

## 11. Terraform Testing

### Using terraform validate
```bash
terraform validate
```

### Using terraform plan
```bash
# Preview changes
terraform plan

# Save plan
terraform plan -out=tfplan

# Review specific resource
terraform plan -target=aws_instance.web
```

### Testing with Terratest (Go)
```go
package test

import (
    "testing"
    "github.com/gruntwork-io/terratest/modules/terraform"
    "github.com/stretchr/testify/assert"
)

func TestTerraformAwsExample(t *testing.T) {
    terraformOptions := &terraform.Options{
        TerraformDir: "../examples/terraform-aws-example",
        Vars: map[string]interface{}{
            "region": "us-east-1",
        },
    }

    defer terraform.Destroy(t, terraformOptions)

    terraform.InitAndApply(t, terraformOptions)

    output := terraform.Output(t, terraformOptions, "instance_id")
    assert.NotEmpty(t, output)
}
```

### Using tflint
```bash
# Install tflint
curl -s https://raw.githubusercontent.com/terraform-linters/tflint/master/install_linux.sh | bash

# Run linting
tflint
```

---

## 12. Terraform Security

### Sensitive Data Handling
```hcl
# Mark variables as sensitive
variable "db_password" {
  type      = string
  sensitive = true
}

# Sensitive outputs
output "db_password" {
  value     = var.db_password
  sensitive = true
}

# Use AWS Secrets Manager
data "aws_secretsmanager_secret_version" "db_password" {
  secret_id = "prod/db/password"
}

resource "aws_db_instance" "main" {
  password = data.aws_secretsmanager_secret_version.db_password.secret_string
}
```

### Encryption
```hcl
# Encrypt S3 backend
terraform {
  backend "s3" {
    bucket         = "my-terraform-state"
    key            = "terraform.tfstate"
    region         = "us-east-1"
    encrypt        = true
    kms_key_id     = "arn:aws:kms:us-east-1:123456789012:key/12345678"
    dynamodb_table = "terraform-locks"
  }
}

# Encrypt EBS volumes
resource "aws_instance" "web" {
  ami           = "ami-12345678"
  instance_type = "t2.micro"

  root_block_device {
    encrypted   = true
    kms_key_id  = aws_kms_key.ebs.arn
  }
}
```

### IAM Least Privilege
```hcl
# Use specific IAM permissions
resource "aws_iam_role_policy" "app" {
  role = aws_iam_role.app.id

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Effect = "Allow"
        Action = [
          "s3:GetObject",
          "s3:PutObject"
        ]
        Resource = "${aws_s3_bucket.app.arn}/*"
      }
    ]
  })}
```

---

## 13. Terraform Troubleshooting

### Common Issues

**Issue: State Lock Error**
```bash
# Force unlock (use carefully!)
terraform force-unlock <LOCK_ID>
```

**Issue: Resource Already Exists**
```bash
# Import existing resource
terraform import aws_instance.web i-1234567890abcdef0
```

**Issue: Dependency Errors**
```hcl
# Explicit dependency
resource "aws_instance" "web" {
  ami           = "ami-12345678"
  instance_type = "t2.micro"
  
  depends_on = [aws_security_group.web]
}
```

**Issue: Cycle Error**
```bash
# Use data sources instead of resource references
data "aws_security_group" "existing" {
  id = "sg-12345678"
}
```

### Debugging
```bash
# Enable debug logging
export TF_LOG=DEBUG
export TF_LOG_PATH=terraform.log

# Disable logging
unset TF_LOG
unset TF_LOG_PATH

# Verbose output
terraform apply -no-color 2>&1 | tee terraform-output.log
```

---

## 14. Interview Questions & Answers

### Basic Questions

**Q: What is Terraform?**
- Infrastructure as Code tool
- Declarative configuration
- Cloud-agnostic
- State management

**Q: Terraform vs Ansible?**
- Terraform: Declarative, infrastructure provisioning
- Ansible: Procedural, configuration management
- Terraform manages state; Ansible doesn't

**Q: Explain Terraform workflow**
1. `terraform init` - Initialize
2. `terraform plan` - Preview
3. `terraform apply` - Create/modify
4. `terraform destroy` - Remove

**Q: What is Terraform state?**
- Maps configuration to real resources
- Stores metadata
- Performance cache
- Tracks dependencies

**Q: What are providers in Terraform?**
- Plugins that interact with APIs
- Examples: AWS, Azure, GCP, Kubernetes
- Downloaded during `terraform init`

### Intermediate Questions

**Q: Difference between count and for_each?**
- **count**: Creates list of resources, referenced by index
- **for_each**: Creates map of resources, referenced by key
- for_each is preferred for dynamic resources

**Q: What are Terraform modules?**
- Reusable Terraform configurations
- Encapsulate resources
- Can be versioned and shared
- Promote DRY principle

**Q: Explain remote state**
- State stored remotely (S3, Azure Storage, GCS)
- Enables team collaboration
- Supports state locking
- More secure than local state

**Q: What is state locking?**
- Prevents concurrent state modifications
- Uses DynamoDB (AWS), Azure Blob Storage, etc.
- Automatically enabled with remote backends
- Prevents corruption and conflicts

**Q: How to manage secrets in Terraform?**
- Use environment variables (TF_VAR_*)
- AWS Secrets Manager / Parameter Store
- HashiCorp Vault
- Mark variables as sensitive
- Never commit secrets to version control

**Q: What are data sources?**
- Read-only information from providers
- Query existing infrastructure
- Examples: AMIs, availability zones, VPCs
- Don't create or modify resources

**Q: Explain terraform import**
```bash
terraform import aws_instance.web i-1234567890abcdef0
```
- Brings existing resources under Terraform management
- Only updates state, doesn't generate configuration
- Must manually write configuration to match

### Advanced Questions

**Q: How does Terraform handle dependencies?**
- **Implicit**: References between resources
- **Explicit**: `depends_on` meta-argument
- Creates dependency graph
- Parallel execution when possible

**Q: Explain Terraform provisioners and why avoid them?**
- Execute scripts on resources
- Types: file, local-exec, remote-exec
- **Avoid because**: Not idempotent, hard to maintain
- **Better alternatives**: User data, configuration management tools

**Q: What is the terraform refresh command?**
- Updates state with real infrastructure
- Detects drift
- Doesn't modify infrastructure
- Automatically run during plan/apply

**Q: Explain terraform taint**
```bash
terraform taint aws_instance.web
terraform apply  # Will recreate
```
- Marks resource for recreation
- Useful when resource is in bad state
- Replaced by `terraform apply -replace` in newer versions

**Q: How to handle multiple environments?**
1. **Workspaces**: Same code, different state
2. **Separate directories**: Complete isolation
3. **Terragrunt**: DRY configuration management
4. **Terraform Cloud**: Workspace management

**Q: What is Terraform Cloud/Enterprise?**
- SaaS platform for Terraform
- Remote state management
- Team collaboration
- Policy as code (Sentinel)
- Private module registry
- Cost estimation

**Q: Explain lifecycle meta-argument**
```hcl
resource "aws_instance" "web" {
  ami           = "ami-12345678"
  instance_type = "t2.micro"

  lifecycle {
    create_before_destroy = true
    prevent_destroy       = true
    ignore_changes        = [tags]
  }
}
```
- `create_before_destroy`: Create new before destroying old
- `prevent_destroy`: Prevent accidental deletion
- `ignore_changes`: Ignore specific attribute changes

**Q: How to handle Terraform state file corruption?**
1. Restore from backup (S3 versioning)
2. Use `terraform state pull` to examine
3. Manually fix with `terraform state` commands
4. Re-import resources if necessary
5. Use `terraform refresh` carefully

**Q: What are Terraform workspaces and when to use them?**
- Separate state files for same configuration
- Use for: dev, staging, prod environments
- **Limitations**: Same backend, limited isolation
- **Better for production**: Separate backends

**Q: Explain dynamic blocks**
```hcl
dynamic "ingress" {
  for_each = var.ingress_rules
  content {
    from_port   = ingress.value.port
    to_port     = ingress.value.port
    protocol    = ingress.value.protocol
    cidr_blocks = ingress.value.cidr_blocks
  }
}
```
- Generate repeated nested blocks
- Based on lists or maps
- Reduces code duplication

**Q: How to perform zero-downtime deployments?**
1. Blue-Green deployment
2. Use `create_before_destroy`
3. Rolling updates with Auto Scaling Groups
4. Canary deployments
5. Use immutable infrastructure

**Q: What is Sentinel in Terraform?**
- Policy as code framework
- Enforces compliance and security
- Available in Terraform Cloud/Enterprise
- Prevents non-compliant changes

**Q: How to manage Terraform at scale?**
1. Use modules extensively
2. Implement proper state management
3. Use Terraform Cloud/Enterprise
4. Automate with CI/CD
5. Implement policy as code
6. Use multiple backends for isolation
7. Implement proper tagging strategy

---

## 15. Terraform CI/CD Integration

### GitLab CI Example

```yaml
# .gitlab-ci.yml
image:
  name: hashicorp/terraform:latest
  entrypoint: [""]

variables:
  TF_ROOT: ${CI_PROJECT_DIR}/terraform
  TF_ADDRESS: ${CI_API_V4_URL}/projects/${CI_PROJECT_ID}/terraform/state/production

cache:
  paths:
    - ${TF_ROOT}/.terraform

before_script:
  - cd ${TF_ROOT}
  - terraform --version
  - terraform init

stages:
  - validate
  - plan
  - apply

validate:
  stage: validate
  script:
    - terraform validate
    - terraform fmt -check

plan:
  stage: plan
  script:
    - terraform plan -out=tfplan
  artifacts:
    paths:
      - ${TF_ROOT}/tfplan
    expire_in: 1 week

apply:
  stage: apply
  script:
    - terraform apply -auto-approve tfplan
  when: manual
  only:
    - main
  dependencies:
    - plan
```

### GitHub Actions Example

```yaml
# .github/workflows/terraform.yml
name: Terraform

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

env:
  TF_VERSION: 1.6.0
  AWS_REGION: us-east-1

jobs:
  terraform:
    name: Terraform
    runs-on: ubuntu-latest
    
    steps:
    - name: Checkout
      uses: actions/checkout@v3

    - name: Setup Terraform
      uses: hashicorp/setup-terraform@v2
      with:
        terraform_version: ${{ env.TF_VERSION }}

    - name: Configure AWS Credentials
      uses: aws-actions/configure-aws-credentials@v2
      with:
        aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
        aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        aws-region: ${{ env.AWS_REGION }}

    - name: Terraform Format
      run: terraform fmt -check

    - name: Terraform Init
      run: terraform init

    - name: Terraform Validate
      run: terraform validate

    - name: Terraform Plan
      run: terraform plan -no-color
      continue-on-error: true

    - name: Terraform Apply
      if: github.ref == 'refs/heads/main' && github.event_name == 'push'
      run: terraform apply -auto-approve
```

### Jenkins Pipeline Example

```groovy
// Jenkinsfile
pipeline {
    agent any
    
    environment {
        TF_VERSION = '1.6.0'
        AWS_REGION = 'us-east-1'
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Setup') {
            steps {
                sh '''
                    wget https://releases.hashicorp.com/terraform/${TF_VERSION}/terraform_${TF_VERSION}_linux_amd64.zip
                    unzip terraform_${TF_VERSION}_linux_amd64.zip
                    sudo mv terraform /usr/local/bin/
                    terraform --version
                '''
            }
        }
        
        stage('Init') {
            steps {
                sh 'terraform init'
            }
        }
        
        stage('Validate') {
            steps {
                sh 'terraform validate'
            }
        }
        
        stage('Plan') {
            steps {
                sh 'terraform plan -out=tfplan'
            }
        }
        
        stage('Apply') {
            when {
                branch 'main'
            }
            steps {
                input message: 'Apply Terraform changes?', ok: 'Apply'
                sh 'terraform apply tfplan'
            }
        }
    }
    
    post {
        always {
            cleanWs()
        }
    }
}
```

---

## 16. Terraform with Other Tools

### Terragrunt (DRY Terraform)

```hcl
# terragrunt.hcl (root)
remote_state {
  backend = "s3"
  generate = {
    path      = "backend.tf"
    if_exists = "overwrite"
  }
  config = {
    bucket         = "my-terraform-state"
    key            = "${path_relative_to_include()}/terraform.tfstate"
    region         = "us-east-1"
    encrypt        = true
    dynamodb_table = "terraform-locks"
  }
}

# environments/dev/terragrunt.hcl
include "root" {
  path = find_in_parent_folders()
}

terraform {
  source = "../../modules//vpc"
}

inputs = {
  environment = "dev"
  vpc_cidr    = "10.0.0.0/16"
}
```

### Terraform with Ansible

```hcl
# Generate Ansible inventory from Terraform
resource "local_file" "ansible_inventory" {
  content = templatefile("${path.module}/inventory.tpl", {
    web_servers = aws_instance.web[*].public_ip
    db_servers  = aws_instance.db[*].private_ip
  })
  filename = "${path.module}/inventory.ini"
}

# inventory.tpl
[web]
%{ for ip in web_servers ~}
${ip}
%{ endfor ~}

[database]
%{ for ip in db_servers ~}
${ip}
%{ endfor ~}
```

### Terraform with Packer

```hcl
# Build AMI with Packer, use in Terraform
data "aws_ami" "app" {
  most_recent = true
  owners      = ["self"]

  filter {
    name   = "name"
    values = ["app-${var.version}-*"]
  }

  filter {
    name   = "tag:Built-By"
    values = ["Packer"]
  }
}

resource "aws_instance" "app" {
  ami           = data.aws_ami.app.id
  instance_type = "t3.medium"
}
```

---

## 17. Terraform Tips & Tricks

### 1. Use Local Values for Complex Expressions

```hcl
locals {
  instance_name = "${var.project}-${var.environment}-${var.component}"
  
  subnet_cidrs = [
    for i in range(var.subnet_count) : 
    cidrsubnet(var.vpc_cidr, 8, i)
  ]
  
  tags = merge(
    var.common_tags,
    {
      Environment = var.environment
      ManagedBy   = "Terraform"
    }
  )
}
```

### 2. Use Conditional Resources

```hcl
resource "aws_eip" "nat" {
  count = var.enable_nat_gateway ? length(var.availability_zones) : 0
  
  vpc = true
  
  tags = {
    Name = "nat-eip-${count.index + 1}"
  }
}
```

### 3. Output Resource Attributes

```hcl
output "instance_details" {
  value = {
    for instance in aws_instance.web :
    instance.id => {
      ip   = instance.public_ip
      dns  = instance.public_dns
      type = instance.instance_type
    }
  }
}
```

### 4. Use templatefile Function

```hcl
resource "aws_instance" "web" {
  user_data = templatefile("${path.module}/user-data.sh", {
    db_host     = aws_db_instance.main.endpoint
    db_name     = var.db_name
    environment = var.environment
  })
}
```

### 5. Organize with Moved Blocks

```hcl
# Refactor without destroying
moved {
  from = aws_instance.old_name
  to   = aws_instance.new_name
}
```

### 6. Use terraform console for Testing

```bash
# Interactive console
terraform console

> var.vpc_cidr
"10.0.0.0/16"

> cidrsubnet(var.vpc_cidr, 8, 0)
"10.0.0.0/24"

> length(["a", "b", "c"])
3
```

### 7. Pre-commit Hooks

```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/antonbabenko/pre-commit-terraform
    rev: v1.81.0
    hooks:
      - id: terraform_fmt
      - id: terraform_validate
      - id: terraform_docs
      - id: terraform_tflint
```

### 8. Use Null Resource for Triggers

```hcl
resource "null_resource" "example" {
  triggers = {
    always_run = timestamp()
  }

  provisioner "local-exec" {
    command = "echo 'Running at ${timestamp()}'"
  }
}
```

---

## 18. Terraform Ecosystem Tools

| Tool | Purpose |
|------|---------|
| **terraform-docs** | Generate documentation |
| **tflint** | Linting and validation |
| **tfsec** | Security scanning |
| **checkov** | Policy checking |
| **infracost** | Cost estimation |
| **terragrunt** | DRY configurations |
| **atlantis** | Pull request automation |
| **terraform-compliance** | BDD testing |
| **terrascan** | Security scanning |
| **pre-commit-terraform** | Git hooks |

### Using terraform-docs

```bash
# Install
brew install terraform-docs

# Generate README
terraform-docs markdown table . > README.md

# Generate in specific format
terraform-docs json . > docs.json
```

### Using tfsec

```bash
# Install
brew install tfsec

# Scan
tfsec .

# Output formats
tfsec . --format json
tfsec . --format junit
```

### Using Infracost

```bash
# Install
brew install infracost

# Show cost estimate
infracost breakdown --path .

# Compare changes
infracost diff --path .
```

---

## 19. Multi-Cloud Example

```hcl
# providers.tf
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~> 3.0"
    }
    google = {
      source  = "hashicorp/google"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = "us-east-1"
}

provider "azurerm" {
  features {}
}

provider "google" {
  project = "my-project"
  region  = "us-central1"
}

# AWS Resources
resource "aws_s3_bucket" "data" {
  bucket = "multi-cloud-data-aws"
}

# Azure Resources
resource "azurerm_storage_account" "data" {
  name                     = "multiclouddata"
  resource_group_name      = azurerm_resource_group.main.name
  location                 = azurerm_resource_group.main.location
  account_tier             = "Standard"
  account_replication_type = "LRS"
}

# GCP Resources
resource "google_storage_bucket" "data" {
  name     = "multi-cloud-data-gcp"
  location = "US"
}
```

---

## 20. Quick Reference Cheat Sheet

```bash
# Initialization
terraform init              # Initialize directory
terraform init -upgrade     # Upgrade providers

# Planning
terraform plan              # Show execution plan
terraform plan -out=plan    # Save plan to file
terraform plan -destroy     # Plan destruction

# Applying
terraform apply             # Apply changes
terraform apply plan        # Apply saved plan
terraform apply -auto-approve  # Skip confirmation
terraform apply -target=resource  # Apply specific resource

# Destroying
terraform destroy           # Destroy all resources
terraform destroy -target=resource  # Destroy specific

# State Management
terraform state list        # List resources
terraform state show resource  # Show resource details
terraform state mv old new  # Rename resource
terraform state rm resource # Remove from state
terraform state pull        # Download state
terraform state push        # Upload state

# Outputs
terraform output            # Show all outputs
terraform output name       # Show specific output

# Workspace
terraform workspace list    # List workspaces
terraform workspace new name  # Create workspace
terraform workspace select name  # Switch workspace

# Validation
terraform validate          # Validate configuration
terraform fmt              # Format code
terraform fmt -recursive   # Format recursively

# Import
terraform import resource id  # Import existing resource

# Debugging
export TF_LOG=DEBUG        # Enable debug logs
export TF_LOG_PATH=terraform.log  # Log to file

# Other
terraform version          # Show version
terraform providers        # Show providers
terraform graph            # Generate dependency graph
terraform show            # Show current state
terraform refresh         # Refresh state
```

---

## 21. Common Errors & Solutions

| Error | Cause | Solution |
|-------|-------|----------|
| Error locking state | Another process running | Wait or force-unlock |
| Resource already exists | Not in state | Import resource |
| Cycle error | Circular dependency | Use data sources |
| Invalid provider | Wrong version | Update version constraint |
| Timeout error | Slow resource creation | Increase timeout |
| Permission denied | IAM permissions | Check credentials/permissions |
| State file corrupt | Interrupted operation | Restore from backup |

---

## 22. Practice Exercises

### Exercise 1: Basic Infrastructure
1. Create VPC with public/private subnets
2. Deploy EC2 instance in public subnet
3. Create RDS in private subnet
4. Set up security groups
5. Output instance IP and DB endpoint

### Exercise 2: Module Development
1. Create reusable VPC module
2. Create compute module for EC2
3. Create database module for RDS
4. Use modules in main configuration
5. Add comprehensive outputs

### Exercise 3: State Management
1. Configure S3 backend
2. Set up state locking with DynamoDB
3. Create multiple workspaces
4. Practice state commands
5. Import existing resource

### Exercise 4: CI/CD Integration
1. Set up GitHub Actions workflow
2. Add validation and planning stages
3. Implement approval for apply
4. Add security scanning with tfsec
5. Generate cost estimates

---

## Resources for Learning

- **Official Documentation**: terraform.io/docs
- **Terraform Registry**: registry.terraform.io
- **HashiCorp Learn**: learn.hashicorp.com/terraform
- **Terraform Best Practices**: github.com/ozbillwang/terraform-best-practices
- **Awesome Terraform**: github.com/shuaibiyy/awesome-terraform
- **Practice Labs**: Katacoda, A Cloud Guru

---

## Terraform Certification Path

### HashiCorp Certified: Terraform Associate
- **Topics**: IaC concepts, Terraform basics, configuration, state, modules
- **Format**: Multiple choice, 57 questions, 60 minutes
- **Passing Score**: 70%
- **Validity**: 2 years

**Study Areas:**
1. Understand IaC concepts
2. Understand Terraform purpose
3. Understand Terraform basics
4. Use Terraform CLI
5. Interact with Terraform modules
6. Navigate Terraform workflow
7. Implement and maintain state
8. Read, generate, and modify configuration
9. Understand Terraform Cloud capabilities

Good luck with your Terraform journey! 🚀
