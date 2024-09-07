# Guide: Using Remote Backend for Terraform with S3 and DynamoDB for State Locking

### Introduction

Using an S3 bucket as a remote backend and DynamoDB for state locking ensures your Terraform state files are stored centrally and that only one user or automation process can apply changes at a time. This setup improves collaboration, stability, and the security of your infrastructure state.

Terraform supports using a remote backend to store state files in S3 and enforce state locking via DynamoDB. This guide explains how to set up both S3 and DynamoDB using either **Terraform** or **AWS CLI**. 

---

## Prerequisites

- **Terraform** installed on your local machine or CI/CD environment.
- **AWS CLI** configured with access to your AWS account.
- Basic knowledge of Terraform and AWS services.

---

## Step 1: Create S3 Bucket for Terraform State

You need an S3 bucket where Terraform will store the state files. Below are two options: using **AWS CLI** or **Terraform**.

### Option 1: Using AWS CLI

1. Open your terminal and run the following command to create the S3 bucket. Replace `<bucket-name>` with your desired bucket name and `<region>` with the AWS region:

```bash
aws s3api create-bucket --bucket <bucket-name> --region <region> --create-bucket-configuration LocationConstraint=<region>
```

Example:

```bash
aws s3api create-bucket --bucket my-terraform-states --region us-east-1 --create-bucket-configuration LocationConstraint=us-east-1
```

2. Enable versioning on the S3 bucket to ensure all changes to the state are tracked:

```bash
aws s3api put-bucket-versioning --bucket <bucket-name> --versioning-configuration Status=Enabled
```

Example:

```bash
aws s3api put-bucket-versioning --bucket my-terraform-states --versioning-configuration Status=Enabled
```

### Option 2: Using Terraform

Create a `s3-bucket.tf` file with the following configuration to provision the S3 bucket via Terraform:

```hcl
resource "aws_s3_bucket" "terraform_state" {
  bucket = "my-terraform-states"
  region = "us-east-1"

  versioning {
    enabled = true
  }

  tags = {
    Name = "TerraformStateBucket"
  }
}

output "s3_bucket_name" {
  value = aws_s3_bucket.terraform_state.bucket
}
```

1. Run the following commands to create the bucket:

```bash
terraform init
terraform apply -auto-approve
```

This will create the S3 bucket and enable versioning for Terraform state management.

---

## Step 2: Create DynamoDB Table for State Locking

Terraform uses a DynamoDB table to lock the state file and avoid conflicts when multiple users apply changes at the same time. Below are the two methods to create the DynamoDB table.

### Option 1: Using AWS CLI

Run the following command to create the DynamoDB table for state locking. Replace `<table-name>` with your desired table name:

```bash
aws dynamodb create-table \
    --table-name <table-name> \
    --attribute-definitions AttributeName=LockID,AttributeType=S \
    --key-schema AttributeName=LockID,KeyType=HASH \
    --billing-mode PAY_PER_REQUEST
```

Example:

```bash
aws dynamodb create-table \
    --table-name terraform-lock-table \
    --attribute-definitions AttributeName=LockID,AttributeType=S \
    --key-schema AttributeName=LockID,KeyType=HASH \
    --billing-mode PAY_PER_REQUEST
```

### Option 2: Using Terraform

Create a `dynamodb-table.tf` file with the following configuration to provision the DynamoDB table via Terraform:

```hcl
resource "aws_dynamodb_table" "terraform_locks" {
  name         = "terraform-lock-table"
  billing_mode = "PAY_PER_REQUEST"
  
  attribute {
    name = "LockID"
    type = "S"
  }

  hash_key = "LockID"

  tags = {
    Name = "TerraformLockTable"
  }
}

output "dynamodb_table_name" {
  value = aws_dynamodb_table.terraform_locks.name
}
```

1. Run the following commands to create the table:

```bash
terraform init
terraform apply -auto-approve
```

This will create the DynamoDB table for state locking in your AWS environment.

---

## Step 3: Configure Terraform Backend

Once the S3 bucket and DynamoDB table are set up, you can configure Terraform to use them as the remote backend for state storage and locking.

1. Create a `backend.tf` file in your Terraform project and add the following configuration:

```hcl
terraform {
  backend "s3" {
    bucket         = "my-terraform-states"      # S3 bucket name
    key            = "my-project/terraform.tfstate" # Path in the S3 bucket
    region         = "us-east-1"                # AWS region
    dynamodb_table = "terraform-lock-table"     # DynamoDB table name
  }
}
```

This configuration tells Terraform to store the state file in the specified S3 bucket and use the DynamoDB table for state locking.

---

## Step 4: Initialize Terraform Backend

After configuring the backend, you need to initialize the Terraform project to connect it to the S3 backend and DynamoDB table.

1. In your terminal, run:

```bash
terraform init
```

Terraform will automatically migrate your local state to the S3 bucket and configure state locking via DynamoDB.

---

## Step 5: Apply Terraform Changes

Once the backend is initialized, you can apply Terraform changes. Terraform will automatically store the state file in S3 and lock the state using the DynamoDB table.

1. Run the following command to apply your Terraform configuration:

```bash
terraform apply
```

---

## Step 6: Verify State in S3 and Locking in DynamoDB

1. **Verify the state file in S3**:
   - Go to the AWS S3 Console, navigate to your bucket (`my-terraform-states`), and check the `my-project/terraform.tfstate` file.

2. **Verify state locking in DynamoDB**:
   - Go to the AWS DynamoDB Console and inspect the `terraform-lock-table`. You'll see a record for the current lock when Terraform is running.

---

