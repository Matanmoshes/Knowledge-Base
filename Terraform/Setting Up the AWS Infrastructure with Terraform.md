Certainly! Here's the complete guide along with all the Terraform configuration files:

### Guide: Setting Up the AWS Infrastructure with Terraform

#### Step 1: Generate an SSH Key Pair
If you don't already have an SSH key pair, generate one using the AWS CLI:

```sh
aws ec2 create-key-pair --key-name your-key-name --query 'KeyMaterial' --output text > ~/.ssh/your-key-name.pem
chmod 400 ~/.ssh/your-key-name.pem
```

Replace `your-key-name` with the desired name for your key pair.

#### Step 2: Save the Terraform Configuration Files
Create a new directory for your project and save the following Terraform configuration files into it.

#### `provider.tf`
```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_key_pair" "webserver-key" {
  key_name   = "webserver-key"
  public_key = file("~/.ssh/id_rsa.pub")
}

data "aws_ssm_parameter" "webserver_ami" {
  name = "/aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2"
}

data "aws_ami" "webserver" {
  most_recent = true
  owners      = ["amazon"]

  filter {
    name   = "name"
    values = [data.aws_ssm_parameter.webserver_ami.value]
  }
}
```

#### `variables.tf`
```hcl
variable "vpc_cidr" {
  default = "10.0.0.0/16"
}

variable "public_subnet_cidr" {
  default = "10.0.1.0/24"
}

variable "private_subnet_cidr" {
  default = "10.0.2.0/24"
}

variable "instance_type" {
  default = "t2.micro"
}

variable "key_name" {
  description = "SSH key pair name"
  default     = "webserver-key"  # Use the key name defined in aws_key_pair resource
}

variable "home_ip" {
  description = "Home IP address for SSH access"
  default     = "62.56.134.54"
}
```

#### `main.tf`
```hcl
resource "aws_vpc" "main" {
  cidr_block = var.vpc_cidr

  tags = {
    Name      = "MainVPC"
    terraform = "true"
  }
}

resource "aws_internet_gateway" "igw" {
  vpc_id = aws_vpc.main.id

  tags = {
    Name      = "InternetGateway"
    terraform = "true"
  }
}

resource "aws_subnet" "public" {
  vpc_id            = aws_vpc.main.id
  cidr_block        = var.public_subnet_cidr
  map_public_ip_on_launch = true

  tags = {
    Name      = "PublicSubnet"
    terraform = "true"
  }
}

resource "aws_subnet" "private" {
  vpc_id            = aws_vpc.main.id
  cidr_block        = var.private_subnet_cidr

  tags = {
    Name      = "PrivateSubnet"
    terraform = "true"
  }
}

resource "aws_eip" "nat" {
  vpc = true

  tags = {
    Name      = "NatEIP"
    terraform = "true"
  }
}

resource "aws_nat_gateway" "nat" {
  allocation_id = aws_eip.nat.id
  subnet_id     = aws_subnet.public.id

  tags = {
    Name      = "NatGateway"
    terraform = "true"
  }
}

resource "aws_route_table" "public" {
  vpc_id = aws_vpc.main.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.igw.id
  }

  tags = {
    Name      = "PublicRouteTable"
    terraform = "true"
  }
}

resource "aws_route_table_association" "public" {
  subnet_id      = aws_subnet.public.id
  route_table_id = aws_route_table.public.id
}

resource "aws_route_table" "private" {
  vpc_id = aws_vpc.main.id

  route {
    cidr_block = "0.0.0.0/0"
    nat_gateway_id = aws_nat_gateway.nat.id
  }

  tags = {
    Name      = "PrivateRouteTable"
    terraform = "true"
  }
}

resource "aws_route_table_association" "private" {
  subnet_id      = aws_subnet.private.id
  route_table_id = aws_route_table.private.id
}

resource "aws_security_group" "public_sg" {
  vpc_id = aws_vpc.main.id

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    from_port   = 443
    to_port     = 443
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = [var.home_ip]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = {
    Name      = "PublicSG"
    terraform = "true"
  }
}

resource "aws_security_group" "private_sg" {
  vpc_id = aws_vpc.main.id

  ingress {
    from_port       = 0
    to_port         = 0
    protocol        = "-1"
    security_groups = [aws_security_group.public_sg.id]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = {
    Name      = "PrivateSG"
    terraform = "true"
  }
}

resource "aws_security_group" "ssh_sg" {
  vpc_id = aws_vpc.main.id

  ingress {
    from_port       = 22
    to_port         = 22
    protocol        = "tcp"
    security_groups = [aws_security_group.public_sg.id]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = {
    Name      = "SshSG"
    terraform = "true"
  }
}
```

#### `ec2.tf`
```hcl
resource "aws_instance" "web" {
  ami           = data.aws_ami.webserver.id
  instance_type = var.instance_type
  subnet_id     = aws_subnet.public.id
  key_name      = aws_key_pair.webserver-key.key_name

  vpc_security_group_ids = [aws_security_group.public_sg.id, aws_security_group.ssh_sg.id]

  user_data = <<-EOF
              #!/bin/bash
              sudo apt-get update
              sudo apt-get install -y nginx
              echo "Welcome to my webserver" | sudo tee /var/www/html/index.html
              sudo systemctl start nginx
              sudo systemctl enable nginx
              EOF

  tags = {
    Name      = "WebServer"
    terraform = "true"
  }
}

resource "aws_instance" "backend" {
  ami           = data.aws_ami.webserver.id
  instance_type = var.instance_type
  subnet_id     = aws_subnet.private.id
  key_name      = aws_key_pair.webserver-key.key_name

  vpc_security_group_ids = [aws_security_group.private_sg.id, aws_security_group.ssh_sg.id]

  tags = {
    Name      = "BackendServer"
    terraform = "true"
  }
}
```

#### `outputs.tf`
```hcl
output "vpc_id" {
  value = aws_vpc.main.id
}

output "public_subnet_id" {
  value = aws_subnet.public.id
}

output "private_subnet_id" {
  value = aws_subnet.private.id
}

output "web_instance_public_ip" {
  value = aws_instance.web.public_ip
}

output "backend_instance_private_ip" {
  value = aws_instance.backend.private_ip
}
```

### Steps to Use

1. **Save the Configuration Files**:
   Ensure all the configuration files (`provider.tf`, `variables.tf`, `main.tf`, `ec2.tf`, `outputs.tf`) are saved in your working directory.

2. **Initialize Terraform**:
   In your project directory, run:
   ```sh
   terraform init
   ```

   This command initializes the Terraform working directory and downloads the necessary provider plugins.

3. **Apply the Configuration**:
   Run the following command to create the infrastructure:
   ```sh
   terraform apply
   ```

   Review the plan and type `yes` to confirm and apply the configuration. This command provisions the specified AWS resources.

### Summary

This guide helps you set up an AWS VPC with public and private subnets, an Internet Gateway, a NAT Gateway, and EC2 instances for a web server and a backend server using Terraform. The web server installs Nginx and creates an `index.html` file with the content "Welcome to my webserver". Adjust the variables as needed for your specific requirements. The configuration uses your existing SSH key pair (`id_rsa` and `id_rsa

.pub`) and retrieves the latest Amazon Linux 2 AMI using the AWS SSM parameter.


```yaml
name: Terraform

on:
  push:
    branches:
      - main  

jobs:
  terraform:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v2

      - name: Set up Terraform
        uses: hashicorp/setup-terraform@v1
        with:
          terraform_version: 1.1.3

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v1
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: us-east-1

      - name: Initialize Terraform
        run: terraform init

      - name: Validate Terraform
        run: terraform validate

      - name: Plan Terraform
        run: terraform plan

      - name: Apply Terraform
        if: github.ref == 'refs/heads/main'  # Only apply on pushes to the main branch
        run: terraform apply -auto-approve

```



---



# AWS Infrastructure with Terraform - Project Guide

This project automates the deployment of a web server infrastructure on AWS using Terraform. It sets up a VPC with public and private subnets, creates security groups, and provisions EC2 instances for a web server and backend server, with automated installation of Nginx on the web server.

## Architecture Structure

```
AWS VPC (10.0.0.0/16)
|
├── Public Subnet (10.0.1.0/24)
│   ├── Internet Gateway
│   ├── NAT Gateway
│   ├── Web Server (EC2 Instance with Nginx)
│   └── Security Group (Public SG)
|
└── Private Subnet (10.0.2.0/24)
    ├── Backend Server (EC2 Instance)
    └── Security Group (Private SG)
|
└── Route Tables
    ├── Public Route Table
    │   ├── 0.0.0.0/0 -> Internet Gateway
    ├── Private Route Table
        ├── 0.0.0.0/0 -> NAT Gateway
```


![[Pasted image 20240805225708.png]]

## Folder Structure

```
terraform-aws-infrastructure/
├── .gitignore
├── .github/
│   └── workflows/
│       └── terraform.yml
├── ec2.tf
├── main.tf
├── outputs.tf
├── provider.tf
├── security_groups.tf
├── variables.tf
├── terraform.tfstate
├── .terraform/
└── .terraform.lock.hcl
```


## Automation with GitHub Actions

This project includes a GitHub Actions workflow that automates the deployment of Terraform whenever changes are pushed to the repository. The workflow is defined in `.github/workflows/terraform.yml` and will automatically run `terraform init`, `terraform validate`, `terraform plan`, and `terraform apply` whenever a push is made to the `main` branch.

## Prerequisites

- An AWS account
- Terraform installed on your local machine
- AWS CLI installed and configured
- SSH key pair for secure access to your EC2 instance

## Key Generation

### Create SSH Keys via CLI

To create SSH keys, use the following commands:

```sh
ssh-keygen -t rsa -b 2048 -f ~/.ssh/id_rsa
```

This command creates a private key (`id_rsa`) and a public key (`id_rsa.pub`) in the `~/.ssh/` directory.

>[!Note]
>When you generate an SSH key pair locally using `ssh-keygen`, it creates a private key (`~/.ssh/id_rsa`) and a public key (`~/.ssh/id_rsa.pub`). However, AWS needs to have a reference to this key pair to allow SSH access to the EC2 instances. The `aws_key_pair` resource uploads your public key to AWS and creates a key pair that AWS can manage.

### Send Keys to the Web Server Machine

To copy your public key to the web server, use the following command:

```sh
scp -i ~/.ssh/id_rsa ~/.ssh/id_rsa.pub ec2-user@<web-server-public-ip>:~/.ssh/
```

Then, SSH into your web server:

```sh
ssh -i ~/.ssh/id_rsa ec2-user@<web-server-public-ip>
```

Set the correct permissions on the public key:

```sh
chmod 400 ~/.ssh/id_rsa.pub
```



## Files Explanation

### `provider.tf`
This file configures the AWS provider and sets up the AWS region and SSH key pair.

### `variables.tf`
Defines the variables used in the configuration, including VPC CIDR block, subnet CIDR blocks, instance type, key name, and AMI ID.

### `main.tf`
Creates the VPC, subnets, internet gateway, NAT gateway, route tables, and associates the subnets with the route tables.

### `security_groups.tf`
Defines the security groups for the public subnet (web server) and private subnet (backend server).

### `ec2.tf`
Creates EC2 instances for the web server and backend server, including the user data script to install Nginx on the web server.

### `outputs.tf`
Outputs the VPC ID, subnet IDs, and the public and private IPs of the EC2 instances.

### `.gitignore`
Specifies files and directories to be ignored by Git.

### `.github/workflows/terraform.yml`
Defines the GitHub Actions workflow for automating Terraform deployment. This workflow runs on every push to the repository and applies the Terraform configuration.

## Running the Project

### Step 1: Clone the repository

```sh
git clone https://github.com/Matanmoshes/terraform-aws-infrastructure.git
```

### Step 2: Configure AWS CLI

Install the AWS CLI (if not already installed):

Follow the installation instructions for your operating system [here](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html).

Configure the AWS CLI:

```sh
aws configure
```

You will be prompted to enter your AWS Access Key ID, Secret Access Key, Default region name, and Default output format. These credentials can be obtained from the AWS Management Console under IAM -> Users -> [Your User] -> Security credentials.

Example Output:

```sh
$ aws configure
AWS Access Key ID [None]: AKIAIOSFODNN7EXAMPLE
AWS Secret Access Key [None]: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
Default region name [None]: us-east-1
Default output format [None]: json
```

### Step 3: Verify AWS CLI Configuration

Verify that your AWS CLI configuration is correct by running:

```sh
aws sts get-caller-identity
```

This command should return details about your AWS account and the IAM user or role you are using.

### Step 4: Generate SSH Key Pair

Generate an SSH key pair if you don't have one:

```sh
ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa
```


This command creates a private key (`id_rsa`) and a public key (`id_rsa.pub`) in the `~/.ssh/` directory.

>[!Note]
>When you generate an SSH key pair locally using `ssh-keygen`, it creates a private key (`~/.ssh/id_rsa`) and a public key (`~/.ssh/id_rsa.pub`). However, AWS needs to have a reference to this key pair to allow SSH access to the EC2 instances. The `aws_key_pair` resource uploads your public key to AWS and creates a key pair that AWS can manage.

### Step 5: Initialize Terraform

Run the following command to initialize Terraform:

```sh
terraform init
```

### Step 6: Validate the Configuration

Validate the Terraform configuration files:

```sh
terraform validate
```

### Step 7: Plan the Infrastructure

Generate and review the execution plan:

```sh
terraform plan
```

### Step 8: Apply the Configuration

Apply the configuration to create the resources:

```sh
terraform apply -auto-approve
```

This command will provision the AWS infrastructure as defined in the Terraform configuration files.

### Step 9: Verify the Deployment

Once the resources are created, you can verify the deployment by SSHing into the web server and backend server as described in the Key Generation section.



---
