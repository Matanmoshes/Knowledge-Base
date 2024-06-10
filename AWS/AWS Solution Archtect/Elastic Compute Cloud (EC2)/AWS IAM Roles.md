### <span style="color:#ffc000">AWS IAM Roles Overview - Key Points</span>

#### <span style="color:#ffc000">Introduction</span>
- **Objective**: Learn about IAM roles and how to use them with EC2 instances.
- **Components**:
  - What is an IAM role?
  - Roles are temporary.
  - Capabilities of roles.
  - Setting up a role in the console.
  - Creating an EC2 instance that assumes the role.
  - Exam tips.

#### <span style="color:#ffc000">What is an IAM Role?</span>
- **IAM Role**: An identity with specific permissions defined by policies.
- **Difference from Users**: Unlike users, roles are not associated with a specific person. They are intended to be assumable by anyone who needs them.
- **Permissions**: Defined using JSON policy documents.
- **Temporary Credentials**: Roles provide temporary security credentials for role sessions.

>[!Note]
>Basically, Roles it's an identity that you can create in IAM that has specific permissions. So role is similar to a user, as it is an AWS identity with permission policies that determine what that identity can and can not do.
#### <span style="color:#ffc000">Capabilities of Roles</span>
- **Assumable by**: People, AWS architecture, or system-level accounts.
- **Cross Account Access**: Allows one AWS account to interact with resources in another AWS account.

#### <span style="color:#ffc000">Setting Up a Role in the Console</span>

1. **Create an IAM Role**:
   - Navigate to **IAM** in the AWS console.
   - Click on **Roles** and then **Create Role**.
   - Select **AWS service** (e.g., EC2).
   - Attach the policy (e.g., AmazonS3FullAccess) to the role.
   - Name the role (e.g., S3Role) and create it.

2. **Launch an EC2 Instance**:
   - Navigate to **EC2** in the AWS console.
   - Launch an instance with the desired configurations.
   - Use an existing key pair and security group.
   - Attach the previously created IAM role to the instance.
   - Connect to the instance using EC2 Instance Connect.

3. **Using the Role on the EC2 Instance**:
   - **Elevate Privileges**: Use `sudo su` to become root.
   - **List S3 Buckets**: `AWS S3 LS` (no need for access keys).
   - **Create S3 Bucket**: `AWS S3 mb s3://bucket-name`.

![[Pasted image 20240608095652.png]]
#### <span style="color:#ffc000">Security Benefits of Roles</span>
- **No Hard-Coded Credentials**: Roles avoid storing access keys on instances, enhancing security.
- **Temporary Access**: If the instance is compromised, attackers cannot use long-term credentials.
- **Revocable Access**: Terminating the instance or removing the role cuts off access.

#### <span style="color:#ffc000">Exam Tips</span>
1. **Preferred Security Option**: Use roles instead of hard-coding access keys.
2. **Policy Documents**: Roles are made up of policy documents controlling permissions.
3. **Immediate Effect**: Updates to role policies take effect immediately.
4. **Attach/Detach Roles**: Can be done without stopping or terminating instances.
5. **Scenario Questions**: Choose roles over access key IDs and secret access keys in exam scenarios.

#### <span style="color:#ffc000">Practical Steps</span>
- **Create and Attach IAM Roles**: Practice setting up and using roles with EC2 instances.
- **Use CLI Commands**: Familiarize with AWS CLI commands to manage S3 buckets via roles.
- **Security Practices**: Emphasize security by avoiding hard-coded credentials and using roles for temporary access.

This summary provides a comprehensive guide to understanding and using AWS IAM roles, essential for securing AWS environments and preparing for the AWS Certified Solutions Architect - Associate exam.