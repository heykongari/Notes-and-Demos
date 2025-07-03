# ❓ **What is AWS CLI?**

The AWS command line interface (cli) is a tool that lets you interact with aws services using commands in your terminal or shell.

Instead of clicking around the aws management console, you can run commands like:
```python
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

# 💿 **AWS Services**

Let's explore most common and widely used aws services like IAM, EC2, S3...

### 🔷 `IAM`: Identity Access and Management
