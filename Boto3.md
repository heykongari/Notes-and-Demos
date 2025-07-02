# 🌸 **Introduction**

Boto3 is an AWS SDK (amazon web services software development kit) for Python. It provides Python API that allows developers to interact with and manage AWS infrastructure and services. Boto3 helps in building applications that leverage various AWS services like EC2, S3, DynamoDB and many more.

# 🔧 **Setup**

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

# **IAM (Identity and Access Management)**

IAM lets you control who access what in AWS. You can manage:
- Users(people)
- Groups
- Roles(Temporary access for services/apps)
- Policies(permissions)

```python
import boto3

# create a client using session.
iam = session.client('iam')

# list users, groups and roles.
users = iam.list_users()

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

# delete user
# access keys must be deleted before deleting a user or this may result in an error.
iam.delete_user(UserName='demo-user')
