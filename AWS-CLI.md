# ❓ **What is AWS CLI?**

The AWS command line interface (cli) is a tool that lets you interact with aws services using commands in your terminal or shell.

Instead of clicking around the aws management console, you can run commands like:
```bash
aws s3 ls
aws ec2 start-instances --instance-ids i-1234567890abcdef0
aws iam create-user --user-name demo-user
```
It works across multiple operating systems and supports scripting which makes it a powerful tool for automation.

# ⭐ **Benefits of AWS CLI in Devops and Automation**

### 1. Speed and Efficiency
- faster than using the web console for repetitive tasks
- easily spin up, configure and tear down environments.

### 2. Automation
- ideal for scripting and automation in CI/CD pipelines.
- can be integrated into Jenkins, Github Actions, Github CI, etc.

### 3. Version Control
- scripts using aws cli can be saved and tracked in Git.

### 4. Remote Management
- Manage your aws resources from any terminal, even programmatically.

# 🔍 **Difference Between AWS CLI, SDK, and Management Console**

| Feature | AWS CLI | AWS SDK | Management Console |
|:-: | :-: | :-: | :-: |
|Interface|Command-line tool|Programming libraries (Python, Java)|Web-based UI|
|Use Case|Manual commands, Shell scripts|Custom app development|Visual management|
|Best For|Devops, scripting, automation|Developers building apps/tools|Beginners, Exploring, Quick checks|
|Languages|Shell(Bash, powershell, etc.)|Python, Java, Node.js, Go, etc.|point and click (GUI)|
|Automation support|Yes (via scripts, cronjobs, CI/CD)|Yes (via code)|No|
|Speed & Flexibility|Fast scripts|Highly flexible(full control via code)|Slower, Manual|

# 💿 **Setup**

> [!IMPORTANT]
> **I'm running my development environment on WSL with Visual Studio Code connected to Ubuntu instance for seamless linux-based coding on Windows.**

### **Installation**
```bash
# make sure your packages are up to date.
sudo apt update and upgrade -y

# download and unpack aws cli.
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install

# verify after installation
aws --version
```

### **Configuration**
```bash
# use this for default configuration
aws configure

# use this if you want named profiles.
aws configure --profile <demo-profile-name>

# You'll be asked for this:
AWS Access Key ID [None]:       <your-access-key-id>
AWS Secret Access Key [None]:   <your-secret-access-key>
Default region name [None]:     <add-region>  # example: us-east-1
Default output format [None]:   <add-format>  # example: json

# verify configuration for default
aws sts get-caller-identity

# verify for named profiles
aws sts get-caller-identity --profile <demo-profile-name>
```

### 🔍 Why use profiles to configure AWS?
- Avoids hardcoding credentials in your scripts or code. Instead, credentials stay securely in `~/.aws/credentials`, reducing the risk of accidental exposure or leaks (e.g., via GitHub).

- Easily switch between environments like development, staging, and production, or between personal and work accounts — all without changing the default settings or reconfiguring credentials each time.

> [!TIP]
> To enable auto-completion, run this command.
```bash
echo "complete -C '/usr/local/bin/aws_completer' aws" >> ~/.bashrc
source ~/.bashrc
```

# 🔽 **AWS Services**

Let's explore most common and widely used aws services like IAM, EC2, S3...

## 🔷 `IAM`: Identity Access and Management

IAM allows you to:
- Create and manage users, groups, roles, and policies.
- Define who can do what on which AWS resources.
- Use fine-grained permissions to secure your infrastructure.

> [!TIP]
> If you use aws profile, you'll need to add `--profile <profile-name>`. To avoid this, use environment variables. Execute this command, `export AWS_PROFILE=<profile-name>`
```bash
# create a new IAM user
aws iam create-user --user-name demo-user

# attach a policy. For example, administrator access policy
aws iam attach-user-policy --user-name demo-user \
--policy-arn arn:aws:iam::aws:policy/AdministratorAccess

# create access key
aws iam create-access-key --user-name demo-user

# create a group
aws iam create-group --group-name demo-group

# add user to group
aws iam add-user-to-group --user-name demo-user --group-name demo-group

# attach policy to group
aws iam attach-group-policy --group-name demo-group \
--policy-arn arn:aws:iam::aws:policy/AmazonEC2FullAccess

# list IAM users, groups, roles and policies
aws iam list-users
aws iam list-groups
aws iam list-roles
aws iam list-policies
```

### 🔸 Custom inline policy
- This type of policy gives you complete control over what a user can access.
- Example, a custom s3-read-only policy. Save it as `custom-s3-read-policy.json`
```json
{
    "Version": "2012-10-17",
    "Statement": [{
        "Effect": "Allow",
        "Action": ["s3:Get*", "s3:List*"],
        "Resource": "*"
    }]
}
```
```bash
# attach custom inline policy
aws iam put-user-policy \
--user-name demo-user \
--policy-name S3ReadOnly \
--policy-document file://custom-s3-read-policy.json
```

### 🔸 Create/Assume a role
- For example, create a role for ec2 access. Save it as `trust-policy.json`
```json
{
    "Version": "2012-10-17",
    "Statement": [{
        "Effect": "Allow",
        "Principal": { "Service": "ec2.amazonaws.com" },
        "Action": "sts:AssumeRole"
    }]
}
```
```bash
# create role and attach policy document
aws iam create-role \
--role-name ec2-s3-access-role \
--assume-role-policy-document file://trust-policy.json

# attach policy to role
aws iam attach-role-policy \
--role-name ec2-s3-access-role \
--policy-arn arn:aws:iam::aws:policy/AmazonS3FullAccess

# Assume role
aws sts-assume-role \
--role-arn arn:aws:iam::123456789012:role/ec2-s3-access-role \
--role-session-name test-session
```

## 🔷 `EC2`: Elastic Compute Cloud
Amazon EC2 provides scalable virtual servers (instances) in the cloud. You can launch, configure, and manage them using the AWS CLI for full automation and DevOps workflows.

### ✅ Here are some free tier eligible AMIs available in the us-east-1 (N. Virginia) region that you can use for practice. Availability may vary depending on when you access them.

|Provider|ID|UserName|Instance Type|
|:-:|:-:|:-:|:-:|
|Amazon Linux|ami-05ffe3c48a9991133|ec2-user|t2.micro|
|Amazon Linux|ami-000ec6c25978d5999|ec2-user|t2.micro|
|Ubuntu|ami-020cba7c55df1f615|ubuntu|t2.micro|
|Ubuntu|ami-0a7d80731ae1b2435|ubuntu|t2.micro|

```bash
# List ec2 instances in your account
aws ec2 describe-instances

# List ec2 AMI using filters and query to narrow output.
aws ec2 describe-images \
  --owners amazon \
  --filters "Name=name,Values=amzn2-ami-hvm-*" "Name=architecture,Values=x86_64" \
  --query 'Images[*].[ImageId,Name]' \
  --output table

# Create a key pair before launching an instance (mandatory)
aws ec2 create-key-pair --key-name my-key --query 'KeyMaterial' --output text > my-key.pem
chmod 400 my-key.pem

# Create a security group and allow ssh access (mandatory)
aws ec2 create-security-group --group-name my-sg --description "My security group"

# Allow ssh access
aws ec2 authorize-security-group-ingress \
  --group-name devops-sg \
  --protocol tcp \
  --port 22 \
  --cidr 0.0.0.0/0

# Allow http access
aws ec2 authorize-security-group-ingress \
  --group-name devops-sg \
  --protocol tcp \
  --port 80 \
  --cidr 0.0.0.0/0

# Launch an Instance
aws ec2 run-instances \
  --image-id <ami-id> \
  --instance-type t2.micro \
  --key-name my-key \
  --security-groups devops-sg \
  --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=DevOps-Server}]'

# Display publicIpAddress of the instance
aws ec2 describe-instances \
  --query "Reservations[*].Instances[*].PublicIpAddress" \
  --output text

# start, stop, reboot and terminate
aws ec2 start-instances --instance-ids i-xxxxxxxxxxxxxxxxx

aws ec2 stop-instances --instance-ids i-xxxxxxxxxxxxxxxxx

aws ec2 reboot-instances --instance-ids i-xxxxxxxxxxxxxxxxx

aws ec2 terminate-instances --instance-ids i-xxxxxxxxxxxxxxxxx
```

## 🔷 `S3`: Simple Storage Service

Amazon S3 is an object storage service that lets you:
- Store files (objects) inside buckets
- Host static websites
- Manage access with permissions and policies
- Enable versioning, encryption, and lifecycle rules

```bash
# create a bucket (bucket name must be globally unique)
aws s3api create-bucket --bucket demo-bucket --region us-east-1

# creating a bucket in regions other than us-east-1
aws s3api create-bucket \
--bucket demo-bucket \
--region <region-code> \
--create-bucket-configuration LocationConstraint=<region-code>

# delete a bucket
aws s3api delete-bucket --bucket demo-bucket
```
>[!WARNING]
>In regions other than `us-east-1`, you must specify `--create-bucket-configuration LocationConstraint=<region-code>`

```bash
# list buckets
aws s3 ls

# upload a single file
aws s3 cp file.txt s3://demo-bucket/

# upload folder recursively
aws s3 cp folder/ s3://demo-bucket/ --recursive

# download files
aws s3 cp s3://demo-bucket/file.txt ./file.txt

# delete a file
aws s3 rm s3://demo-bucket/file.txt