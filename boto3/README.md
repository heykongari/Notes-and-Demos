# 🌸 Introduction

Boto3 is an AWS SDK (amazon web services software development kit) for Python. It provides Python API that allows developers to interact with and manage AWS infrastructure and services. Boto3 helps in building applications that leverage various AWS services like EC2, S3, DynamoDB and many more.

# 🔧 Setup

🚨 **I'm running my development environment on WSL with Visual Studio Code connected to Ubuntu instance for seamless linux-based coding on Windows.**

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

# ✒️ Fundamentals

## 🔷 `Session`: Entry-Level point

A session represents your connection settings to AWS.It handles yours credentials, default region and profile.

### ✍️ Example

```python
import boto3

# create a session with your named profile
session = boto3.Session(profile_name='my-profile')
```

If you don't create one, boto3 will use the default session profile based on environment variables or AWS config files.

## 🔷 `client`: Low-Level service access

A client is a direct connection to an AWS service. All API operations are available in a dictionary-style way.

### ✍️ Example

```python
# create client using a session
demo_client = session.cient('s3')

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

## 🔷 `resource`: High-Level, object-oriented

A resource is a higher-level abstraction over client. It wraps AWS operations into more pythonic, objbect-like interfaces.

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

### 🆚 Quick Comparision
| Feature | Session | client | resource |
| - | - | - | - |
| Purpose | set aws config | direct API access | high-level, pythonic |
| Style | - |low-level, dictionary style |objbect-oriented |
| Control | full config control | all AWS API operations | simplified API |
| Availability | all services |all services |limited services |
| Use Case | starting point | scripts and automation | cleaner, readable |
