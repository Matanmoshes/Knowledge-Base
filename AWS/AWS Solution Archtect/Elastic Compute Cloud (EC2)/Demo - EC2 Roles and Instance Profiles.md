### EC2 Roles and Instance Profiles Guide

**Introduction**
AWS Identity and Access Management (IAM) roles for Amazon Elastic Compute Cloud (EC2) provide the ability to grant instances temporary credentials. These temporary credentials can be used by hosted applications to access permissions configured within the role. IAM roles eliminate the need for managing credentials, help mitigate long-term security risks, and simplify permissions management.

This guide will walk you through the process of setting up IAM roles and instance profiles using both the AWS CLI and the AWS Management Console. 

**Prerequisites:**
- AWS Management Console access
- Basic knowledge of EC2 and IAM
- AWS CLI installed and configured

![[Pasted image 20240614160100.png]]

### Solution

**1. Log in to the AWS Management Console**
- Ensure you are using the `us-east-1` (N. Virginia) region.

**2. Create a Trust Policy and Role Using the AWS CLI**

**Obtain the labreferences.txt File:**
1. Navigate to S3 in the AWS Management Console.
2. Open the bucket containing `s3bucketlookupfiles` in its name.
3. Download the `labreferences.txt` file.

**Log in to Bastion Host and Set the AWS CLI Region and Output Type:**
1. Navigate to EC2 > Instances.
2. Copy the public IP of the Bastion Host instance.
3. Open a terminal and log in to the bastion host via SSH:
   ```bash
   ssh cloud_user@<BASTION_HOST_PUBLIC_IP>
   ```
4. Configure the AWS CLI:
   ```bash
   aws configure
   ```
   - Press Enter twice to leave the AWS Access Key ID and AWS Secret Access Key blank.
   - Enter `us-east-1` as the default region name.
   - Enter `json` as the default output format.

**Create IAM Trust Policy for an EC2 Role:**
1. Create a file called `trust_policy_ec2.json`:
   ```bash
   nano trust_policy_ec2.json
   ```
2. Paste the following content into the file:
   ```json
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Principal": {"Service": "ec2.amazonaws.com"},
         "Action": "sts:AssumeRole"
       }
     ]
   }
   ```
3. Save and quit the file.

**Create the DEV_ROLE IAM Role:**
1. Run the following AWS CLI command:
   ```bash
   aws iam create-role --role-name DEV_ROLE --assume-role-policy-document file://trust_policy_ec2.json
   ```

**Create an IAM Policy Defining Read-Only Access Permissions to an S3 Bucket:**
1. Create a file called `dev_s3_read_access.json`:
   ```bash
   nano dev_s3_read_access.json
   ```
2. Enter the following content, replacing `<DEV_S3_BUCKET_NAME>` with the bucket name from the `labreferences.txt` file:
   ```json
   {
       "Version": "2012-10-17",
       "Statement": [
           {
             "Sid": "AllowUserToSeeBucketListInTheConsole",
             "Action": ["s3:ListAllMyBuckets", "s3:GetBucketLocation"],
             "Effect": "Allow",
             "Resource": ["arn:aws:s3:::*"]
           },
           {
               "Effect": "Allow",
               "Action": [
                   "s3:Get*",
                   "s3:List*"
               ],
               "Resource": [
                   "arn:aws:s3:::<DEV_S3_BUCKET_NAME>/*",
                   "arn:aws:s3:::<DEV_S3_BUCKET_NAME>"
               ]
           }
       ]
   }
   ```
3. Save and quit the file.

**Create the Managed Policy:**
1. Run the following command:
   ```bash
   aws iam create-policy --policy-name DevS3ReadAccess --policy-document file://dev_s3_read_access.json
   ```
2. Copy the policy ARN from the output and paste it into the `labreferences.txt` file.

**Create Instance Profile and Attach Role to an EC2 Instance:**

**Attach Managed Policy to Role:**
1. Run the following command, replacing `<DevS3ReadAccess_POLICY_ARN>` with the copied ARN:
   ```bash
   aws iam attach-role-policy --role-name DEV_ROLE --policy-arn "<DevS3ReadAccess_POLICY_ARN>"
   ```
2. Verify the managed policy was attached:
   ```bash
   aws iam list-attached-role-policies --role-name DEV_ROLE
   ```

**Create the Instance Profile and Add the DEV_ROLE via the AWS CLI:**
1. Create instance profile named DEV_PROFILE:
   ```bash
   aws iam create-instance-profile --instance-profile-name DEV_PROFILE
   ```
2. Add the role to the DEV_PROFILE:
   ```bash
   aws iam add-role-to-instance-profile --instance-profile-name DEV_PROFILE --role-name DEV_ROLE
   ```
3. Verify the configuration:
   ```bash
   aws iam get-instance-profile --instance-profile-name DEV_PROFILE
   ```

**Attach the DEV_PROFILE Role to an Instance:**
1. Navigate to EC2 > Instances in the AWS console.
2. Copy the instance ID of the instance named Web Server instance and paste it into the `labreferences.txt` file.
3. In the terminal, attach the DEV_PROFILE to the instance:
   ```bash
   aws ec2 associate-iam-instance-profile --instance-id <LAB_WEB_SERVER_INSTANCE_ID> --iam-instance-profile Name="DEV_PROFILE"
   ```
4. Verify the configuration:
   ```bash
   aws ec2 describe-instances --instance-ids <LAB_WEB_SERVER_INSTANCE_ID>
   ```

**Test S3 Permissions via the AWS CLI:**
1. Copy the public IP of the Web Server instance.
2. Open a new terminal and log in to the web server instance via SSH:
   ```bash
   ssh cloud_user@<WEB_SERVER_PUBLIC_IP>
   ```
3. Verify the instance is assuming the DEV_ROLE role:
   ```bash
   aws sts get-caller-identity
   ```
4. List the buckets in the account:
   ```bash
   aws s3 ls
   ```
5. Attempt to view the files in the `s3bucketdev-` bucket:
   ```bash
   aws s3 ls s3://<s3bucketdev-123>
   ```

**Create an IAM Policy and Role Using the AWS Management Console:**

**Create Policy:**
1. Navigate to IAM > Policies in the AWS console.
2. Click Create policy.
3. Click the JSON tab.
4. Paste the following text as the policy, replacing `<PROD_S3_BUCKET_NAME>` with the bucket name from the `labreferences.txt` file:
   ```json
   {
       "Version": "2012-10-17",
       "Statement": [
           {
             "Sid": "AllowUserToSeeBucketListInTheConsole",
             "Action": ["s3:ListAllMyBuckets", "s3:GetBucketLocation"],
             "Effect": "Allow",
             "Resource": ["arn:aws:s3:::*"]
           },
           {
               "Effect": "Allow",
               "Action": [
                   "s3:Get*",
                   "s3:List*"
               ],
               "Resource": [
                   "arn:aws:s3:::<PROD_S3_BUCKET_NAME>/*",
                   "arn:aws:s3:::<PROD_S3_BUCKET_NAME>"
               ]
           }
       ]
   }
   ```
5. Click Next.
6. Enter `ProdS3ReadAccess` as the policy name.
7. Click Create policy.

**Create Role:**
1. Click Roles in the left-hand menu.
2. Click Create role.
3. Under Choose a use case, select EC2.
4. Click Next.
5. In the Filter policies search box, enter `ProdS3ReadAccess`.
6. Click the checkbox to select `ProdS3ReadAccess`.
7. Click Next.
8. Give it a Role name of `PROD_ROLE`.
9. Click Create role.

**Attach IAM Role to an EC2 Instance Using the AWS Management Console:**
1. Navigate to EC2 > Instances.
2. Select the Web Server instance.
3. Click Actions > Security > Modify IAM role.
4. In the IAM role dropdown, select `PROD_ROLE`.
5. Click Update IAM role.

**Test the Configuration:**
1. Open the existing terminal connected to the Web Server instance (reconnect if needed).
2. Determine the identity currently being used:
   ```bash
   aws sts get-caller-identity
   ```
3. List the buckets:
   ```bash
   aws s3 ls
   ```
4. Attempt to view the files in the `s3bucketprod-` bucket:
   ```bash
   aws s3 ls s3://<s3bucketprod-123>
   ```
5. Attempt to view the files in the `s3bucketsecret-` bucket:
   ```bash
   aws s3 ls s3://<s3bucketsecret-123>
   ```
   This should result in access being denied.

**Conclusion**
Congratulations on successfully completing this hands-on lab! You have learned how to use IAM roles and instance profiles to manage temporary credentials for EC2 instances, and how to attach policies to control access to AWS services.