# AWS Basics Complete Guide for DevOps

## 1. AWS Fundamentals

### What is AWS?
- **Amazon Web Services** - Cloud computing platform by Amazon
- Launched in **2006**
- Provides **200+ services**
- Pay-as-you-go pricing model
- Global infrastructure with **30+ regions**

### Cloud Computing Models

| Model | Description | Examples |
|-------|-------------|----------|
| **IaaS** | Infrastructure as a Service | EC2, VPC, S3 |
| **PaaS** | Platform as a Service | Elastic Beanstalk, RDS |
| **SaaS** | Software as a Service | WorkMail, Chime |

### AWS Global Infrastructure

```
AWS Global Infrastructure
├── Regions (30+)
│   ├── Availability Zones (2-6 per region)
│   │   └── Data Centers (multiple per AZ)
│   └── Local Zones
├── Edge Locations (400+)
└── Regional Edge Caches
```

**Region**: Geographic area (us-east-1, eu-west-1, ap-south-1)  
**Availability Zone**: Isolated data centers within region  
**Edge Location**: CDN endpoints for CloudFront

---

## 2. AWS Core Services

### Compute Services

#### EC2 (Elastic Compute Cloud)

**What is EC2?**
- Virtual servers in the cloud
- Scalable computing capacity
- Multiple instance types and sizes

**Instance Types:**

| Family | Purpose | Examples |
|--------|---------|----------|
| **General Purpose** | Balanced compute, memory, network | t2, t3, m5 |
| **Compute Optimized** | High-performance processors | c5, c6g |
| **Memory Optimized** | Large datasets in memory | r5, x1, z1d |
| **Storage Optimized** | High sequential read/write | i3, d2, h1 |
| **Accelerated Computing** | GPU instances | p3, g4, f1 |

**EC2 Pricing Models:**

```
1. On-Demand
   - Pay per hour/second
   - No commitment
   - Highest cost

2. Reserved Instances
   - 1 or 3-year commitment
   - Up to 75% discount
   - Types: Standard, Convertible, Scheduled

3. Spot Instances
   - Bid for unused capacity
   - Up to 90% discount
   - Can be terminated by AWS

4. Dedicated Hosts
   - Physical server dedicated to you
   - Compliance requirements
   - Most expensive

5. Savings Plans
   - Flexible pricing
   - Commitment to compute usage
   - Up to 72% discount
```

**Launching EC2 Instance (Console):**
1. Choose AMI (Amazon Machine Image)
2. Select instance type
3. Configure instance details
4. Add storage
5. Add tags
6. Configure security group
7. Review and launch
8. Select/create key pair

**User Data Script Example:**
```bash
#!/bin/bash
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd
echo "<h1>Hello from EC2</h1>" > /var/www/html/index.html
```

#### Lambda (Serverless Computing)

**What is Lambda?**
- Run code without managing servers
- Pay only for compute time
- Automatic scaling
- Event-driven execution

**Use Cases:**
- Data processing
- Real-time file processing
- Backend for web/mobile apps
- Scheduled tasks

**Lambda Limits:**
- Memory: 128 MB to 10 GB
- Timeout: 15 minutes max
- Deployment package: 50 MB (zipped), 250 MB (unzipped)

**Simple Lambda Function (Python):**
```python
import json

def lambda_handler(event, context):
    name = event.get('name', 'World')
    return {
        'statusCode': 200,
        'body': json.dumps(f'Hello {name}!')
    }
```

---

### Storage Services

#### S3 (Simple Storage Service)

**What is S3?**
- Object storage service
- Unlimited storage
- 99.999999999% (11 9's) durability
- Stores objects in buckets

**S3 Storage Classes:**

| Class | Use Case | Retrieval Time | Cost |
|-------|----------|----------------|------|
| **Standard** | Frequently accessed | Milliseconds | Highest |
| **Intelligent-Tiering** | Unknown access patterns | Milliseconds | Auto-optimized |
| **Standard-IA** | Infrequent access | Milliseconds | Lower |
| **One Zone-IA** | Infrequent, non-critical | Milliseconds | Lowest IA |
| **Glacier Instant** | Archive, instant access | Milliseconds | Lower |
| **Glacier Flexible** | Archive, minutes-hours | 1-5 minutes | Very low |
| **Glacier Deep Archive** | Long-term archive | 12 hours | Lowest |

**S3 Bucket Operations (AWS CLI):**
```bash
# Create bucket
aws s3 mb s3://my-bucket-name

# List buckets
aws s3 ls

# Upload file
aws s3 cp file.txt s3://my-bucket/
aws s3 cp folder/ s3://my-bucket/ --recursive

# Download file
aws s3 cp s3://my-bucket/file.txt .

# Sync directory
aws s3 sync ./local-folder s3://my-bucket/

# Delete file
aws s3 rm s3://my-bucket/file.txt

# Delete bucket (must be empty)
aws s3 rb s3://my-bucket
aws s3 rb s3://my-bucket --force  # Delete with contents
```

**S3 Versioning:**
- Keep multiple versions of objects
- Protect against accidental deletion
- Can be suspended but not disabled

**S3 Lifecycle Policies:**
```json
{
  "Rules": [
    {
      "Id": "Move to IA after 30 days",
      "Status": "Enabled",
      "Transitions": [
        {
          "Days": 30,
          "StorageClass": "STANDARD_IA"
        },
        {
          "Days": 90,
          "StorageClass": "GLACIER"
        }
      ]
    }
  ]
}
```

#### EBS (Elastic Block Store)

**What is EBS?**
- Block storage for EC2 instances
- Persistent storage
- Automatically replicated within AZ
- Can be backed up to S3 (snapshots)

**EBS Volume Types:**

| Type | Description | IOPS | Throughput | Use Case |
|------|-------------|------|------------|----------|
| **gp3** | General Purpose SSD | 3,000-16,000 | 125-1000 MB/s | Boot volumes, dev/test |
| **gp2** | General Purpose SSD | 3-16,000 | Up to 250 MB/s | General purpose |
| **io2** | Provisioned IOPS SSD | 100-64,000 | Up to 1,000 MB/s | Databases |
| **st1** | Throughput Optimized HDD | 500 | Up to 500 MB/s | Big data, logs |
| **sc1** | Cold HDD | 250 | Up to 250 MB/s | Infrequent access |

**EBS Operations (AWS CLI):**
```bash
# Create volume
aws ec2 create-volume --size 100 --volume-type gp3 --availability-zone us-east-1a

# Attach to instance
aws ec2 attach-volume --volume-id vol-xxx --instance-id i-xxx --device /dev/sdf

# Create snapshot
aws ec2 create-snapshot --volume-id vol-xxx --description "My backup"

# List snapshots
aws ec2 describe-snapshots --owner-ids self

# Delete volume
aws ec2 delete-volume --volume-id vol-xxx
```

#### EFS (Elastic File System)

**What is EFS?**
- Managed NFS file system
- Shared storage for multiple EC2 instances
- Automatic scaling
- Pay for what you use

**EFS vs EBS:**
- **EBS**: Block storage, single instance attachment
- **EFS**: File storage, multiple instance attachment

---

### Database Services

#### RDS (Relational Database Service)

**What is RDS?**
- Managed relational database
- Automated backups, patching, scaling
- Multi-AZ deployment for HA

**Supported Engines:**
- Amazon Aurora (MySQL/PostgreSQL compatible)
- MySQL
- PostgreSQL
- MariaDB
- Oracle
- SQL Server

**RDS Features:**
- **Automated Backups**: Point-in-time recovery
- **Read Replicas**: Scale read operations
- **Multi-AZ**: High availability
- **Encryption**: At rest and in transit

**Creating RDS Instance (AWS CLI):**
```bash
aws rds create-db-instance \
    --db-instance-identifier mydb \
    --db-instance-class db.t3.micro \
    --engine mysql \
    --master-username admin \
    --master-user-password MyPassword123 \
    --allocated-storage 20 \
    --backup-retention-period 7 \
    --multi-az
```

#### DynamoDB

**What is DynamoDB?**
- Fully managed NoSQL database
- Single-digit millisecond latency
- Automatic scaling
- Serverless

**Key Concepts:**
- **Table**: Collection of items
- **Item**: Collection of attributes (like row)
- **Attribute**: Key-value pair (like column)
- **Primary Key**: Partition key (required) + Sort key (optional)

**DynamoDB Operations (AWS CLI):**
```bash
# Create table
aws dynamodb create-table \
    --table-name Users \
    --attribute-definitions AttributeName=UserId,AttributeType=S \
    --key-schema AttributeName=UserId,KeyType=HASH \
    --billing-mode PAY_PER_REQUEST

# Put item
aws dynamodb put-item \
    --table-name Users \
    --item '{"UserId":{"S":"user1"},"Name":{"S":"John Doe"}}'

# Get item
aws dynamodb get-item \
    --table-name Users \
    --key '{"UserId":{"S":"user1"}}'

# Scan table
aws dynamodb scan --table-name Users

# Delete table
aws dynamodb delete-table --table-name Users
```

---

### Networking Services

#### VPC (Virtual Private Cloud)

**What is VPC?**
- Isolated virtual network
- Complete control over network configuration
- Subnets, route tables, gateways

**VPC Components:**

```
VPC (10.0.0.0/16)
├── Public Subnet (10.0.1.0/24)
│   ├── Internet Gateway
│   └── Public route table
├── Private Subnet (10.0.2.0/24)
│   ├── NAT Gateway
│   └── Private route table
├── Security Groups (Stateful)
└── Network ACLs (Stateless)
```

**Creating VPC (AWS CLI):**
```bash
# Create VPC
aws ec2 create-vpc --cidr-block 10.0.0.0/16

# Create subnet
aws ec2 create-subnet --vpc-id vpc-xxx --cidr-block 10.0.1.0/24

# Create Internet Gateway
aws ec2 create-internet-gateway

# Attach IGW to VPC
aws ec2 attach-internet-gateway --vpc-id vpc-xxx --internet-gateway-id igw-xxx

# Create route table
aws ec2 create-route-table --vpc-id vpc-xxx

# Add route to IGW
aws ec2 create-route --route-table-id rtb-xxx --destination-cidr-block 0.0.0.0/0 --gateway-id igw-xxx
```

**Security Groups vs NACLs:**

| Feature | Security Group | Network ACL |
|---------|---------------|-------------|
| Level | Instance | Subnet |
| State | Stateful | Stateless |
| Rules | Allow only | Allow and Deny |
| Evaluation | All rules | Rules in order |

#### Route 53 (DNS Service)

**What is Route 53?**
- Managed DNS service
- Domain registration
- Health checking
- Traffic routing

**Routing Policies:**
- **Simple**: Single resource
- **Weighted**: Distribute traffic by percentage
- **Latency**: Route to lowest latency
- **Failover**: Active-passive setup
- **Geolocation**: Route based on user location
- **Geoproximity**: Route based on geographic location
- **Multi-value**: Return multiple IPs

#### CloudFront (CDN)

**What is CloudFront?**
- Content Delivery Network
- Low latency content delivery
- DDoS protection
- Integrates with AWS Shield and WAF

**Use Cases:**
- Static website hosting
- Video streaming
- API acceleration
- Software distribution

---

## 3. AWS Security & Identity

### IAM (Identity and Access Management)

**What is IAM?**
- Manage access to AWS resources
- Users, Groups, Roles, Policies
- Free service
- Global service (not region-specific)

**IAM Components:**

```
IAM Structure
├── Users
│   └── Individual identities
├── Groups
│   └── Collection of users
├── Roles
│   └── Assumed by services or users
└── Policies
    └── JSON documents defining permissions
```

**IAM Policy Example:**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Resource": "arn:aws:s3:::my-bucket/*"
    },
    {
      "Effect": "Deny",
      "Action": "s3:DeleteBucket",
      "Resource": "*"
    }
  ]
}
```

**IAM Best Practices:**
1. Enable MFA for root account
2. Create individual IAM users
3. Use groups to assign permissions
4. Grant least privilege
5. Use roles for applications
6. Rotate credentials regularly
7. Enable CloudTrail logging
8. Use policy conditions for extra security

**IAM Operations (AWS CLI):**
```bash
# Create user
aws iam create-user --user-name john

# Create group
aws iam create-group --group-name Developers

# Add user to group
aws iam add-user-to-group --user-name john --group-name Developers

# Attach policy to group
aws iam attach-group-policy \
    --group-name Developers \
    --policy-arn arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess

# Create access key
aws iam create-access-key --user-name john

# List users
aws iam list-users

# Create role
aws iam create-role \
    --role-name EC2-S3-Access \
    --assume-role-policy-document file://trust-policy.json
```

### AWS Security Services

#### Security Groups
- Virtual firewall for instances
- Control inbound and outbound traffic
- Stateful (return traffic automatically allowed)

**Example Security Group Rules:**
```
Inbound:
- SSH (22) from 203.0.113.0/24
- HTTP (80) from 0.0.0.0/0
- HTTPS (443) from 0.0.0.0/0

Outbound:
- All traffic to 0.0.0.0/0
```

#### AWS Shield
- DDoS protection
- **Standard**: Automatic, free
- **Advanced**: Enhanced protection, $3000/month

#### AWS WAF (Web Application Firewall)
- Protect web applications
- Filter HTTP/HTTPS requests
- Rules for SQL injection, XSS, etc.

#### AWS Secrets Manager
- Store and rotate secrets
- Automatic rotation for RDS
- Fine-grained access control

```bash
# Create secret
aws secretsmanager create-secret \
    --name MyDatabasePassword \
    --secret-string '{"username":"admin","password":"MyP@ssw0rd"}'

# Retrieve secret
aws secretsmanager get-secret-value --secret-id MyDatabasePassword
```

---

## 4. AWS Management & Monitoring

### CloudWatch

**What is CloudWatch?**
- Monitoring and observability service
- Collect and track metrics
- Collect and monitor log files
- Set alarms and automate actions

**Key Features:**
- **Metrics**: CPU, Network, Disk, Custom metrics
- **Logs**: Centralized log management
- **Alarms**: Trigger actions based on thresholds
- **Events**: Respond to state changes
- **Dashboards**: Visualize metrics

**CloudWatch Alarms (AWS CLI):**
```bash
# Create alarm
aws cloudwatch put-metric-alarm \
    --alarm-name cpu-high \
    --alarm-description "Alert when CPU exceeds 80%" \
    --metric-name CPUUtilization \
    --namespace AWS/EC2 \
    --statistic Average \
    --period 300 \
    --threshold 80 \
    --comparison-operator GreaterThanThreshold \
    --evaluation-periods 2

# List alarms
aws cloudwatch describe-alarms

# Delete alarm
aws cloudwatch delete-alarms --alarm-names cpu-high
```

**Custom Metrics:**
```bash
# Put custom metric
aws cloudwatch put-metric-data \
    --namespace MyApp \
    --metric-name ActiveUsers \
    --value 125 \
    --timestamp 2024-01-15T12:00:00Z
```

### CloudTrail

**What is CloudTrail?**
- AWS API logging service
- Track user activity and API usage
- Compliance and auditing
- Detect security issues

**What CloudTrail Logs:**
- Who made the request
- When was it made
- What action was performed
- Which resources were affected
- From where (IP address)

### AWS Systems Manager

**What is Systems Manager?**
- Unified interface for AWS resources
- Automate operational tasks
- Patch management
- Run commands on instances

**Key Features:**
- **Session Manager**: Browser-based shell
- **Parameter Store**: Store configuration
- **Patch Manager**: Automate patching
- **Run Command**: Execute commands remotely

```bash
# Store parameter
aws ssm put-parameter \
    --name /myapp/database/password \
    --value "MyPassword123" \
    --type SecureString

# Get parameter
aws ssm get-parameter \
    --name /myapp/database/password \
    --with-decryption
```

---

## 5. AWS CLI Basics

### Installation

```bash
# Linux
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install

# macOS
brew install awscli

# Windows
# Download and run MSI installer from AWS website

# Verify installation
aws --version
```

### Configuration

```bash
# Configure credentials
aws configure
# AWS Access Key ID: YOUR_ACCESS_KEY
# AWS Secret Access Key: YOUR_SECRET_KEY
# Default region: us-east-1
# Default output format: json

# Configure profile
aws configure --profile dev

# Use profile
aws s3 ls --profile dev

# Environment variables
export AWS_ACCESS_KEY_ID=YOUR_ACCESS_KEY
export AWS_SECRET_ACCESS_KEY=YOUR_SECRET_KEY
export AWS_DEFAULT_REGION=us-east-1
```

### Common AWS CLI Commands

```bash
# EC2
aws ec2 describe-instances
aws ec2 start-instances --instance-ids i-xxx
aws ec2 stop-instances --instance-ids i-xxx
aws ec2 terminate-instances --instance-ids i-xxx

# S3
aws s3 ls
aws s3 cp file.txt s3://bucket/
aws s3 sync folder/ s3://bucket/

# IAM
aws iam list-users
aws iam create-user --user-name john
aws iam list-access-keys --user-name john

# RDS
aws rds describe-db-instances
aws rds create-db-snapshot --db-instance-identifier mydb --db-snapshot-identifier mydb-snapshot

# Lambda
aws lambda list-functions
aws lambda invoke --function-name my-function output.txt

# CloudWatch
aws cloudwatch describe-alarms
aws logs describe-log-groups

# Output formats
aws ec2 describe-instances --output json
aws ec2 describe-instances --output table
aws ec2 describe-instances --output text

# Query and filter
aws ec2 describe-instances \
    --query 'Reservations[*].Instances[*].[InstanceId,State.Name,InstanceType]' \
    --output table

# Dry run
aws ec2 terminate-instances --instance-ids i-xxx --dry-run
```

---

## 6. AWS Well-Architected Framework

### Five Pillars

#### 1. Operational Excellence
- Run and monitor systems
- Continuously improve processes
- **Principles**: IaC, documentation, frequent small changes

#### 2. Security
- Protect information and systems
- **Principles**: Strong identity foundation, traceability, defense in depth, least privilege

#### 3. Reliability
- Recover from failures
- Meet demand
- **Principles**: Auto-scaling, backup, multi-AZ deployment

#### 4. Performance Efficiency
- Use computing resources efficiently
- **Principles**: Right-sizing, serverless, caching

#### 5. Cost Optimization
- Avoid unnecessary costs
- **Principles**: Pay for what you use, Reserved Instances, monitoring

---

## 7. Common AWS Architecture Patterns

### 3-Tier Web Application

```
Users
  │
  ▼
CloudFront (CDN)
  │
  ▼
Application Load Balancer
  │
  ├──────────┬──────────┐
  ▼          ▼          ▼
EC2 (Web)  EC2 (Web)  EC2 (Web)
  │
  ▼
RDS (Multi-AZ)
  │
  ▼
S3 (Static Assets)
```

### Serverless Architecture

```
API Gateway
  │
  ▼
Lambda Functions
  │
  ├──────────┬──────────┐
  ▼          ▼          ▼
DynamoDB   S3    SNS/SQS
```

### High Availability Architecture

```
Route 53 (DNS)
  │
  ├────────────────────┐
  ▼                    ▼
Region 1            Region 2
  │                    │
  ▼                    ▼
Multi-AZ RDS      Multi-AZ RDS
Auto Scaling      Auto Scaling
  │                    │
  ▼                    ▼
S3 (Cross-Region Replication)
```

---

## 8. AWS Interview Questions

### Basic Questions

**Q: What is AWS?**
- Cloud computing platform offering 200+ services
- Pay-as-you-go model
- Global infrastructure
- On-demand resources

**Q: What is a Region and Availability Zone?**
- **Region**: Geographic area (us-east-1)
- **Availability Zone**: Isolated data center within region
- Each region has multiple AZs for high availability

**Q: Difference between S3 and EBS?**
- **S3**: Object storage, unlimited, accessed via HTTP
- **EBS**: Block storage, attached to EC2, limited by size

**Q: What is the difference between public and private subnets?**
- **Public**: Has route to Internet Gateway, internet access
- **Private**: No direct internet access, uses NAT Gateway

**Q: What is an AMI?**
- Amazon Machine Image
- Template for EC2 instances
- Contains OS, application, configuration

### Intermediate Questions

**Q: Explain different EC2 pricing models**
- On-Demand: Pay per hour
- Reserved: 1-3 year commitment, up to 75% discount
- Spot: Bid for unused capacity, up to 90% discount
- Dedicated: Physical server, compliance

**Q: What is Auto Scaling?**
- Automatically adjust number of EC2 instances
- Based on demand or schedule
- Ensures high availability and cost optimization

**Q: Difference between Security Group and NACL?**
| Feature | Security Group | NACL |
|---------|---------------|------|
| Level | Instance | Subnet |
| State | Stateful | Stateless |
| Rules | Allow only | Allow/Deny |

**Q: What is CloudFormation?**
- Infrastructure as Code service
- Define resources in JSON/YAML templates
- Version control infrastructure
- Automate deployment

**Q: Explain RDS Multi-AZ deployment**
- Synchronous replication to standby in different AZ
- Automatic failover
- For high availability, not scaling

### Advanced Questions

**Q: How to secure S3 bucket?**
1. Block public access
2. Enable encryption (SSE-S3, SSE-KMS)
3. Bucket policies and IAM roles
4. Enable versioning
5. Enable access logging
6. Use VPC endpoints
7. Enable MFA delete

**Q: Explain AWS shared responsibility model**
- **AWS**: Security OF the cloud (infrastructure)
- **Customer**: Security IN the cloud (data, applications)

**Q: How to reduce AWS costs?**
1. Right-size instances
2. Use Reserved Instances/Savings Plans
3. Use Spot Instances
4. Delete unused resources
5. Use S3 lifecycle policies
6. Enable Auto Scaling
7. Use CloudFront for content delivery
8. Monitor with Cost Explorer

**Q: What is the difference between ELB types?**
- **ALB**: HTTP/HTTPS, Layer 7, path-based routing
- **NLB**: TCP/UDP, Layer 4, high performance
- **CLB**: Legacy, Layer 4/7
- **GLB**: Layer 3, transparent network gateway

---

## 9. AWS Best Practices

### Security
- Enable MFA on root account
- Use IAM roles instead of access keys
- Implement least privilege
- Encrypt data at rest and in transit
- Enable CloudTrail logging
- Use VPC for network isolation

### Cost Optimization
- Right-size resources
- Use Auto Scaling
- Leverage Reserved Instances
- Delete unused resources
- Use S3 lifecycle policies
- Monitor with AWS Cost Explorer

### Reliability
- Deploy across multiple AZs
- Use Auto Scaling
- Implement backup strategies
- Use Route 53 health checks
- Test disaster recovery

### Performance
- Use CDN (CloudFront)
- Implement caching (ElastiCache)
- Use appropriate instance types
- Optimize database queries
- Use read replicas

---

## Quick Reference

```bash
# EC2
aws ec2 describe-instances
aws ec2 start-instances --instance-ids i-xxx
aws ec2 stop-instances --instance-ids i-xxx

# S3
aws s3 ls
aws s3 cp file.txt s3://bucket/
aws s3 sync folder/ s3://bucket/

# IAM
aws iam list-users
aws iam create-user --user-name john

# RDS
aws rds describe-db-instances
aws rds create-db-snapshot

# Lambda
aws lambda list-functions
aws lambda invoke --function-name my-function

# CloudWatch
aws cloudwatch describe-alarms
aws logs tail /aws/lambda/my-function --follow
```

Good luck with AWS! ☁️
