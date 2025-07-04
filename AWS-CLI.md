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

# 🔽 **AWS Services**

Let's explore most common and widely used aws services like IAM, EC2, S3...

### 🔷 `IAM`: Identity Access and Management

IAM allows you to:
- Create and manage users, groups, roles, and policies.
- Define who can do what on which AWS resources.
- Use fine-grained permissions to secure your infrastructure.

