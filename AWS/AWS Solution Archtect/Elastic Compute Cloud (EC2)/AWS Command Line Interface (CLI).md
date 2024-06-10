### AWS Command Line Interface (CLI) Overview - Key Points

#### <span style="color:#ffc000">Introduction</span>
- **Objective**: Learn how to interact with AWS using the command line.
- **AWS Management Console**: GUI method for AWS interaction, point-and-click provisioning.
- **Command Line**: Alternative method, interacting with AWS by typing commands.

#### <span style="color:#ffc000">Lesson Objectives</span>
1. Launch an EC2 instance using the command line.
2. Create an IAM user with S3 access.
3. Configure the AWS CLI with user credentials.
4. Use the CLI to create an S3 bucket and upload a file.

![[Pasted image 20240607181708.png]]

#### <span style="color:#ffc000">Step-by-Step Process</span>

1. **Launching an EC2 Instance via Console**:
   - Navigate to the **AWS console** and select **EC2** under Compute.
   - Launch an instance, name it (e.g., MyWebServer), and configure settings.
   - Select an existing security group (e.g., launch-wizard-1).
   - Use an existing key pair for SSH access.
   - Connect to the instance using **EC2 Instance Connect**.
   - Elevate privileges with `sudo su` and update with `dnf update -y`.

2. **Using the AWS Command Line**:
   - **AWS Command**: Start with `AWS`, specify the service (e.g., S3), and the action (e.g., LS for list).
   - Example: `AWS S3 LS` lists all S3 buckets.
   - **Configuration**: If credentials are not configured, use `AWS configure`.
     - Enter AWS access key, secret access key, region, and output format.

3. **Configuring IAM for CLI Access**:
   - Navigate to **IAM** in the AWS console.
   - **Create a User Group** with S3 admin access.
     - Example: Admin-S3 with the policy AmazonS3FullAccess.
   - **Create a User** (e.g., AlexSmith) with programmatic access.
     - Add the user to the Admin-S3 group.
     - Generate access keys for the user.

4. **Using the CLI for S3 Operations**:
   - Configure the CLI with generated access keys using `AWS configure`.
   - **List Buckets**: `AWS S3 LS` to see all S3 buckets.
   - **Create a Bucket**: `AWS S3 MB s3://bucket-name` to create a new bucket.
   - **Verify**: Use `AWS S3 LS` to ensure the bucket is created.

```aws
[root@ip-172-31-17-43 ec2-user]# aws s3 ls
Unable to locate credentials. You can configure credentials by running "aws configure".

[root@ip-172-31-17-43 ec2-user]# aws configure
AWS Access Key ID [None]: example Access Key ID
AWS Secret Access Key [None]: example Secret Access
Default region name [None]: 
Default output format [None]: 

[root@ip-172-31-17-43 ec2-user]# aws s3 ls

[root@ip-172-31-17-43 ec2-user]# aws s3 mb s3://dsfkljsd45jkljfgrej34
make_bucket: dsfkljsd45jkljfgrej34

[root@ip-172-31-17-43 ec2-user]# aws s3 ls
2024-06-08 06:32:59 dsfkljsd45jkljfgrej34

[root@ip-172-31-17-43 ec2-user]# 
```

#### <span style="color:#ffc000">Exam Tips</span>
1. **Principle of Least Privilege**:
   - Give users the minimum necessary access.
   - Use IAM groups to manage permissions effectively.

2. **Managing Access Keys**:
   - Treat access keys like passwords, don't share them.
   - Each user should have their own access key ID and secret access key.
   - If an access key is lost, delete and regenerate it, then reconfigure the CLI.

3. **CLI Compatibility**:
   - Supported on **Linux, Windows, and macOS**.
   - Can be used on both local machines and EC2 instances.

#### <span style="color:#ffc000">Practical Steps</span>
- **Launch an EC2 Instance**: Ensure you can start and configure instances.
- **Create IAM Users and Groups**: Manage permissions and security.
- **Configure the CLI**: Set up and use the CLI for various AWS services.
- **S3 Operations**: Create and manage S3 buckets using the command line.

This summary provides a comprehensive guide to using the AWS Command Line Interface, essential for efficiently managing AWS resources and preparing for the AWS Certified Solutions Architect - Associate exam.