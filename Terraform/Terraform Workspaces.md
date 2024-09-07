# Guide: Using Terraform Workspaces to Manage State Across Different AWS Accounts or Environments

### Introduction

Terraform workspaces provide an effective way to manage separate states for different environments or AWS accounts within a single configuration. This guide will walk you through the steps to set up and manage Terraform workspaces to handle multiple AWS accounts or environments like dev, prod, and testing.

---

### Prerequisites

- **Terraform installed** on your local machine or CI environment.
- **AWS CLI configured** with credentials for both the private and lab AWS accounts.
- Access to a **Terraform configuration** that manages AWS resources.

---

### Step 1: Initialize Terraform

Before working with workspaces, initialize your Terraform project by running the following command in your project's root directory:

```bash
terraform init
```

This command initializes the backend and installs any required providers.

---

### Step 2: Create a New Workspace

By default, Terraform operates in the `default` workspace. To create a new workspace for a different environment or AWS account, run:

```bash
terraform workspace new <workspace-name>
```

For example, if you want to create a workspace for your **private AWS account**, you can name the workspace `private-account`:

```bash
terraform workspace new private-account
```

Similarly, if you want to create a workspace for your **lab AWS account**, you can name the workspace `lab-account`:

```bash
terraform workspace new lab-account
```

---

### Step 3: Switch Between Workspaces

To switch between workspaces, use the following command:

```bash
terraform workspace select <workspace-name>
```

For instance, to switch from your current workspace to the `lab-account` workspace:

```bash
terraform workspace select lab-account
```

You can confirm the active workspace by running:

```bash
terraform workspace show
```

This will display the name of the current workspace you're using.

---

### Step 4: Use Workspace-Specific State

Each workspace will maintain its own state file. This means when you switch workspaces, Terraform will manage resources independently for each environment or AWS account.

For example:
- In the `private-account` workspace, you might manage production resources.
- In the `lab-account` workspace, you might manage temporary resources for testing.

---

### Step 5: Apply Changes to Each Workspace

When you apply your Terraform configuration, it will only affect the resources defined for the current workspace.

To apply changes in the **private account** workspace:

```bash
terraform workspace select private-account
terraform apply
```

To apply changes in the **lab account** workspace:

```bash
terraform workspace select lab-account
terraform apply
```

This ensures that changes in one environment do not impact the other.

---

### Step 6: Managing S3 and DynamoDB for State Locking (Optional)

If you're working across multiple AWS accounts and want to ensure state management consistency and locking, consider setting up an **S3 bucket** and **DynamoDB table** in one of your AWS accounts (e.g., the private account) for managing the Terraform state files.

In your `backend.tf` file:

```hcl
terraform {
  backend "s3" {
    bucket         = "my-terraform-states"
    key            = "project-name/terraform.tfstate"
    region         = "us-east-1"
    dynamodb_table = "terraform-lock-table"
  }
}
```

This will ensure state files are saved and locked securely when working across different workspaces.

---

### Step 7: List Available Workspaces

To see a list of all the available workspaces, use:

```bash
terraform workspace list
```

---

### Conclusion

Terraform workspaces provide a clean and scalable way to manage infrastructure across different environments or accounts without needing multiple configuration files. By using workspaces, you can maintain separate states and easily switch between environments like production, development, and testing.

