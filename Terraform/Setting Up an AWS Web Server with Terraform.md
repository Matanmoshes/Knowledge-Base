
# Guide to Setting Up an AWS Web Server with Terraform

This guide walks you through the steps to create and bootstrap a web server on AWS using Terraform. It covers generating SSH keys and configuring Terraform files.

## Prerequisites

- An AWS account
- Terraform installed on your local machine
- SSH key pair for secure access to your EC2 instance

## Step 1: Generate SSH Key Pair

Generate an SSH key pair if you don't have one:

```sh
ssh-keygen -t rsa -b 2048 -f ~/.ssh/id_rsa
```

This command creates a private key (`id_rsa`) and a public key (`id_rsa.pub`) in the `~/.ssh/` directory.

## Step 2: Create `setup.tf`

Create a file named `setup.tf` with the following content. This file sets up the VPC, subnet, security groups, and uploads your SSH public key to AWS to create a key pair:

```hcl
provider "aws" {
  region = "us-east-1"
}

# Create an SSH key pair for EC2 instance access
resource "aws_key_pair" "webserver-key" {
  key_name   = "webserver-key"
  public_key = file("~/.ssh/id_rsa.pub")
}

data "aws_ssm_parameter" "webserver-ami" {
  name = "/aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2"
}

resource "aws_vpc" "vpc" {
  cidr_block           = "10.0.0.0/16"
  enable_dns_support   = true
  enable_dns_hostnames = true
  tags = {
    Name = "terraform-vpc"
  }
}

resource "aws_internet_gateway" "igw" {
  vpc_id = aws_vpc.vpc.id
}

data "aws_route_table" "main_route_table" {
  filter {
    name   = "association.main"
    values = ["true"]
  }
  filter {
    name   = "vpc-id"
    values = [aws_vpc.vpc.id]
  }
}

resource "aws_default_route_table" "internet_route" {
  default_route_table_id = data.aws_route_table.main_route_table.id
  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.igw.id
  }
  tags = {
    Name = "Terraform-RouteTable"
  }
}

data "aws_availability_zones" "azs" {
  state = "available"
}

resource "aws_subnet" "subnet" {
  availability_zone = element(data.aws_availability_zones.azs.names, 0)
  vpc_id            = aws_vpc.vpc.id
  cidr_block        = "10.0.1.0/24"
}

resource "aws_security_group" "sg" {
  name        = "sg"
  description = "Allow TCP/80 & TCP/22"
  vpc_id      = aws_vpc.vpc.id
  ingress {
    description = "Allow SSH traffic"
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  ingress {
    description = "Allow HTTP traffic"
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

output "Webserver-Public-IP" {
  value = aws_instance.webserver.public_ip
}
```

### Explanation of `setup.tf`

- **`provider "aws"`**: Configures the AWS provider and region.
- **`resource "aws_key_pair" "webserver-key"`**: Creates an SSH key pair resource in AWS using your locally generated public key.
- **`data "aws_ssm_parameter" "webserver-ami"`**: Fetches the latest Amazon Linux 2 AMI ID using SSM Parameter Store.
- **`resource "aws_vpc" "vpc"`**: Creates a VPC with DNS support and hostname enabled.
- **`resource "aws_internet_gateway" "igw"`**: Creates an Internet Gateway for the VPC.
- **`data "aws_route_table" "main_route_table"`**: Fetches the main route table associated with the VPC.
- **`resource "aws_default_route_table" "internet_route"`**: Creates a default route table to route internet traffic through the Internet Gateway.
- **`data "aws_availability_zones" "azs"`**: Gets all available availability zones in the region.
- **`resource "aws_subnet" "subnet"`**: Creates a subnet in the first availability zone.
- **`resource "aws_security_group" "sg"`**: Creates a security group to allow HTTP and SSH access.
- **`output "Webserver-Public-IP"`**: Outputs the public IP address of the web server.

## Step 3: Create `main.tf`

Create a file named `main.tf` with the following content. This file configures the creation and bootstrapping of an EC2 instance:

```hcl
resource "aws_instance" "webserver" {
  ami                         = data.aws_ssm_parameter.webserver-ami.value
  instance_type               = "t3.micro"
  key_name                    = aws_key_pair.webserver-key.key_name
  associate_public_ip_address = true
  vpc_security_group_ids      = [aws_security_group.sg.id]
  subnet_id                   = aws_subnet.subnet.id

  provisioner "remote-exec" {
    inline = [
      "sudo yum -y install httpd && sudo systemctl start httpd",
      "echo '<h1><center>My Test Website With Help From Terraform Provisioner</center></h1>' > index.html",
      "sudo mv index.html /var/www/html/"
    ]
    connection {
      type        = "ssh"
      user        = "ec2-user"
      private_key = file("~/.ssh/id_rsa")
      host        = self.public_ip
    }
  }

  tags = {
    Name = "webserver"
  }
}
```

### Explanation of `main.tf`

- **`resource "aws_instance" "webserver"`**: Defines an EC2 instance resource named `webserver`.
- **`ami`**: Uses the latest Amazon Linux 2 AMI.
- **`instance_type`**: Specifies the instance type as `t3.micro`.
- **`key_name`**: References the key pair created in AWS using your locally generated public key.
- **`associate_public_ip_address`**: Ensures the instance gets a public IP address.
- **`vpc_security_group_ids`**: Attaches the security group for network access.
- **`subnet_id`**: Places the instance in the specified subnet.
- **`provisioner "remote-exec"`**: Runs commands on the instance after launch to install and start the Apache HTTP server and set up a test webpage.
- **`tags`**: Adds a name tag to the instance for identification.

## Step 4: Initialize and Validate Terraform Configuration

1. **Initialize Terraform**:
   ```sh
   terraform init
   ```

2. **Validate the Configuration**:
   ```sh
   terraform validate
   ```

   This checks the syntax and consistency of the configuration files.

## Step 5: Plan and Apply the Configuration

1. **Generate and Review the Execution Plan**:
   ```sh
   terraform plan
   ```

   This creates an execution plan, showing what actions Terraform will take to create or modify infrastructure.

2. **Apply the Configuration**:
   ```sh
   terraform apply
   ```

   This will prompt you to confirm the plan. Type `yes` to proceed. Terraform will then create all the necessary resources, including the EC2 instance.

## Step 6: Access Your Web Server

Once the `terraform apply` command completes, it will output the public IP address of your web server. You can access your web server by navigating to this IP address in your web browser.

```sh
output "Webserver-Public-IP" {
  value = aws_instance.webserver.public_ip
}
```

### Example Output

```sh
Webserver-Public-IP = "54.123.45.67"
```

Visit `http://54.123.45.67` in your web browser to see your test website.

