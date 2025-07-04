# ❓ **What is Boto3?**

Boto3 is an AWS SDK (amazon web services software development kit) for Python. It provides Python API that allows developers to interact with and manage AWS infrastructure and services. Boto3 helps in building applications that leverage various AWS services like EC2, S3, DynamoDB and many more.

# 💿 **Setup**

> [!IMPORTANT]
> **I'm running my development environment on WSL with Visual Studio Code connected to Ubuntu instance for seamless linux-based coding on Windows.**

- Install Boto3: `pip install boto3`
- Configure credentials using `--profile` options:

```bash
aws configure --profile <any-profile-name>

# You'll be asked for this:
AWS Access Key ID [None]:       <your-access-key-id>
AWS Secret Access Key [None]:   <your-secret-access-key>
Default region name [None]:     <add-region>  # example: us-east-1
Default output format [None]:   <add-format>  # example: json
```

### 🔍 Why use profiles to configure AWS?
- Avoids hardcoding credentials in your scripts or code. Instead, credentials stay securely in `~/.aws/credentials`, reducing the risk of accidental exposure or leaks (e.g., via GitHub).

- Easily switch between environments like development, staging, and production, or between personal and work accounts — all without changing the default settings or reconfiguring credentials each time.

# ✒️ **Fundamentals**

### 🔷 `Session`: Entry-Level point

A session represents your connection settings to AWS.It handles yours credentials, default region and profile.

### ✍️ Example

```python
import boto3

# create a session with your named profile
session = boto3.Session(profile_name='my-profile')
```

If you don't create one, boto3 will use the default session profile based on environment variables or AWS config files.

### 🔷 `client`: Low-Level service access

A client is a direct connection to an AWS service. All API operations are available in a dictionary-style way.

### ✍️ Example

```python
# create client using a session
demo_client = session.client('s3')

# task: list all buckets
response = demo_client.list_buckets()

# list bucket by name
for bucket in response["Buckets"]:
    print(bucket['Name'])
```

### 🔍 Why use it?
- Full access to all service features, including latest ones
- Lightweight and faster
- Great for scripts and automation

### 🔷 `resource`: High-Level, object-oriented

A resource is a higher-level abstraction over client. It wraps AWS operations into more pythonic, object-like interfaces.

### ✍️ Example

```python
# create resource using a session
demo_resource = session.resource('s3')

# get a bucket and upload a file
bucket = demo_resource.Bucket('your-bucket')
bucket.upload_file('local_file.txt', 'remote_file.txt')
```

### 🔍 Why use it?
- Easier to read and write
- Great for common tasks like EC2 and S3
- Limited services

### 🆚 Quick Comparison
| Feature | client | resource |
|:-:|:-:|:-:|
| Style | low-level, dictionary style |high-level, object-oriented |
| Verbosity | more parameters, more configurations | cleaner and python-like |
| Use Case | full control and config | quick scripts and iterating resources |
| Availability | all services |limited services |
| Example | `s3.list_buckets()` | `s3.buckets.all()` |

# 🔽 **AWS Services**

Let's explore the most common and widely used aws services like S3, EC2, IAM...

### 🔷 `S3`: Simple Storage Service

What is Amazon S3?
- Amazon s3 (simple storage service) is an object storage service.
- You can store files (called objects) inside buckets (like folders).
- Each object has a key (its name), and a value (its data).

```python
import boto3

# create a client using session.
s3 = session.client('s3')

# list buckets.
s3.list_buckets()

# create a new bucket
# bucket names must be globally unique.
s3.create_bucket(
    Bucket='demo-bucket',
    CreateBucketConfiguration={'LocationConstraint': 'ap-south-1'} # optional
)

# upload a file to bucket
s3.upload_file(
    Filename='sample_file.txt',
    Bucket='demo-bucket',
    Key='uploaded_file.txt' # Key represents file name in the bucket
)

# list files in a bucket
response = s3.list_objects_v2(Bucket='demo-bucket')

if 'Contents' in response:
    for obj in response['Contents']:
        print(obj['Key'])
else:
    print("Bucket is empty")

# download files from a bucket
s3.download_file(
    Filename='downloaded_file.txt',
    Bucket='demo-bucket',
    Key='uploaded_file.txt'
)

# delete object
s3.delete_object(
    Bucket='demo-bucket',
    Key='uploaded_file.txt'
)

# delete bucket
s3.delete_bucket(Bucket='demo-bucket')
```

### 🔷 `EC2`: Elastic Compute Cloud

EC2 (Elastic Compute Cloud) – one of the most powerful services in AWS. Automating EC2 using Boto3 is very useful for real-world tasks like auto-scaling, launching servers, and managing infrastructure.

```python
import boto3

# create a client using session
ec2 = session.client('ec2')

# list instances
response = ec2.describe_instances()

for reservation in response['Reservations']:
    for instance in reservation['Instances']:
        print(f"ID: {instance['InstanceId']}, State: {instance['State']['Name']}")

# list images (AMI: amazon machine images)
response = ec2.describe_images(Owners=['amazon'])

for image in response['Images'][:5]: # list 5 images
    print(f"AMI ID: {image['ImageId']}, Name: {image['Name']}")
```

> [!IMPORTANT]
> Before creating a new instance, a valid key pair and a security group must be created.

```python
# create a key pair (for ssh login)
demo_key_pair = ec2.create_key_pair(KeyName='demo-key')

# save to file
with open('demo-key.pem', 'w') as f:
    f.write(demo_key_pair['KeyMaterial'])

# create a security group
demo_group = ec2.create_security_group(
    GroupName='demo-sg',
    Description='security group for boto3 demo'
)

# allow ssh access
ec2.authorize_security_group_ingress(
    GroupId=demo_group['GroupId'],
    IpPermissions=[
        {
            'IpProtocol': 'tcp',
            'FromPort': 22,
            'ToPort': 22,
            'IpRanges': [{'CidrIp': '0.0.0.0/0'}]
        }
    ]
)
```

> [!TIP]
> AWS automatically creates a default security group. If you choose to use it, you must allow inbound traffic on port 22 to enable SSH access.

```python
# create a new instance
new_instance = ec2.run_instances(
    ImageId='<enter a valid image id here>',
    InstanceType='t2.micro',
    KeyName='demo-key',
    MinCount=1,
    MaxCount=1,
    SecurityGroupIds=[demo-group['GroupId']],
    TagSpecifications=[
        {
            'ResourceType': 'instance',
            'Tags': [{'Key': 'Name', 'Value': 'DemoBoto3Instance'}]
        }
    ],
    UserData='''#!/bin/bash
    echo "Hello from Boto3" > /home/ec2-user/boto3.txt'''
)

# start, stop, reboot and terminate
ec2.start_instances(InstanceIds=['<enter image id here>'])

ec2.stop_instances(InstanceIds=['<enter image id here>'])

ec2.reboot_instances(InstanceIds=['<enter image id here>'])

ec2.terminate_instances(InstanceIds=['<enter image id here>'])
```

### 🔷 `IAM`: Identity and Access Management

IAM lets you control who access what in AWS. You can manage:
- Users(people)
- Groups
- Roles(Temporary access for services/apps)
- Policies(permissions)

```python
import boto3

# create a client using session.
iam = session.client('iam')

# list users.
users = iam.list_users()

# print all the users and their info.
for user in users['Users']:
    print(f"Username: {user['UserName']}, Created: {user['CreateDate']}")

# list groups and roles.
groups = iam.list_groups()

roles = iam.list_roles()

# create new user
new_user = iam.create_user(UserName='demo-user')

# create access keys
new_access_key = iam.create_access_key(UserName='demo-user')

# attach and detach policies
iam.attach_user_policy(
    UserName='demo-user',
    PolicyArn='arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess'
)

iam.detach_user_policy(
    UserName='demo-user',
    PolicyArn='arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess'
)

# delete user.
# access keys must be deleted before deleting a user or this may result in an error.
iam.delete_user(UserName='demo-user')
```
