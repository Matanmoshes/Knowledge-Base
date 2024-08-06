## Introduction to Terraform

Terraform is an open-source Infrastructure as Code (IaC) tool developed by HashiCorp that allows you to define and provision infrastructure using a high-level configuration language. Terraform supports various cloud providers such as AWS, Azure, Google Cloud, and many others. It enables the creation, management, and versioning of infrastructure efficiently and consistently.

## How Terraform Works

Terraform uses a simple syntax to define your infrastructure in configuration files. It then uses these configuration files to create an execution plan, which describes the steps Terraform will take to achieve the desired state of your infrastructure.

### Key Concepts

1. **Providers**: These are plugins that interact with APIs of cloud providers (like AWS, Azure), SaaS providers, and other services. You need to specify the provider in your configuration.
2. **Resources**: These are the fundamental building blocks of your infrastructure. Each resource corresponds to a service or component, such as an AWS S3 bucket or an Azure Virtual Machine.
3. **State**: Terraform keeps track of the infrastructure it manages in a state file. This state file is crucial for mapping real-world resources to your configuration, keeping track of metadata, and improving performance.
4. **Modules**: These are containers for multiple resources that are used together. Modules allow you to create reusable and shareable configurations.
5. **Variables**: These are used to parameterize your configurations, making them flexible and reusable.

## Required Files and Basic Syntax

### 1. `main.tf`

This file contains the primary configuration for your infrastructure. Here's an example of a simple AWS S3 bucket configuration:

```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_s3_bucket" "example" {
  bucket = "my-unique-bucket-name"
  acl    = "private"
}
```

### 2. `variables.tf`

This file is used to define input variables for your configuration. For example:

```hcl
variable "bucket_name" {
  description = "The name of the S3 bucket"
  type        = string
}

variable "region" {
  description = "The AWS region"
  type        = string
  default     = "us-east-1"
}
```

### 3. `outputs.tf`

This file defines the outputs of your configuration, which are values that are useful to be displayed upon the successful completion of the Terraform apply. For example:

```hcl
output "bucket_arn" {
  value = aws_s3_bucket.example.arn
}
```

### 4. `terraform.tfvars`

This file is used to define the values for the input variables. For example:

```hcl
bucket_name = "my-unique-bucket-name"
region      = "us-east-1"
```

### Basic Commands

1. **Initialize Terraform**: Run this command in your configuration directory to initialize the working directory containing Terraform configuration files.
   ```sh
   terraform init
   ```

2. **Plan**: Run this command to create an execution plan. This plan shows the actions that will be taken to reach the desired state.
   ```sh
   terraform plan
   ```

3. **Apply**: Run this command to apply the changes required to reach the desired state of the configuration.
   ```sh
   terraform apply
   ```

4. **Destroy**: Run this command to destroy the infrastructure managed by Terraform.
   ```sh
   terraform destroy
   ```

### Example Workflow

1. **Write Configuration**: Create `main.tf`, `variables.tf`, and `outputs.tf` files with the necessary configurations.
2. **Initialize Directory**: Run `terraform init` to set up the working directory.
3. **Create Execution Plan**: Run `terraform plan` to see what changes will be made.
4. **Apply Configuration**: Run `terraform apply` to create the infrastructure.
5. **Inspect Outputs**: After the apply is complete, inspect the output values.
6. **Manage Changes**: Update configurations as needed and run `terraform apply` to apply updates.
7. **Destroy Infrastructure**: Run `terraform destroy` when you no longer need the infrastructure.

## Detailed Guide Example

Let's consider a more comprehensive example where you use Terraform to create an S3 bucket and a DynamoDB table.

### `main.tf`

```hcl
provider "aws" {
  region = var.region
}

resource "aws_s3_bucket" "example" {
  bucket = var.bucket_name
  acl    = "private"
}

resource "aws_dynamodb_table" "example" {
  name         = var.dynamodb_table_name
  billing_mode = "PAY_PER_REQUEST"
  hash_key     = "id"

  attribute {
    name = "id"
    type = "S"
  }
}
```

### `variables.tf`

```hcl
variable "bucket_name" {
  description = "The name of the S3 bucket"
  type        = string
}

variable "dynamodb_table_name" {
  description = "The name of the DynamoDB table"
  type        = string
}

variable "region" {
  description = "The AWS region"
  type        = string
  default     = "us-east-1"
}
```

### `outputs.tf`

```hcl
output "bucket_arn" {
  value = aws_s3_bucket.example.arn
}

output "dynamodb_table_arn" {
  value = aws_dynamodb_table.example.arn
}
```

### `terraform.tfvars`

```hcl
bucket_name         = "my-unique-bucket-name"
dynamodb_table_name = "my-dynamodb-table"
region              = "us-east-1"
```

### Steps to Deploy

1. **Initialize Terraform**: Run `terraform init`.
2. **Create Execution Plan**: Run `terraform plan`.
3. **Apply Configuration**: Run `terraform apply`.
4. **Inspect Outputs**: After the apply is complete, inspect the output values.
5. **Manage Changes**: Update configurations as needed and run `terraform apply` to apply updates.
6. **Destroy Infrastructure**: Run `terraform destroy` when you no longer need the infrastructure.

This example demonstrates how to manage multiple resources using Terraform. By following this guide, you can create, manage, and destroy infrastructure efficiently using Terraform.

---

# References:

Terraform documentation
https://registry.terraform.io/providers/hashicorp/aws/latest/docs