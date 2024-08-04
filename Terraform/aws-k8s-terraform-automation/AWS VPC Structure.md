Yes, the provided `main.tf` file includes the setup for the Kubernetes cluster using `kubeadm` with the `user_data` scripts to install Docker, Kubernetes components, and initialize the Kubernetes cluster.

Here's a recap of the structure and configuration:

### AWS VPC Structure

**AWS VPC (10.0.0.0/16)**

```
AWS VPC (10.0.0.0/16)
|
├── Public Subnet (10.0.1.0/24)
│   ├── Internet Gateway
│   ├── NAT Gateway
│   └── Bastion Host (EC2 Instance)
|
└── Private Subnet (10.0.2.0/24)
    ├── Kubernetes Control Plane Node (EC2 Instance)
    └── Kubernetes Worker Nodes (2 EC2 Instances)

```

---
### Detailed Explanation of `main.tf` File

#### Provider Configuration
	
```hcl
provider "aws" {
  region = "us-east-1"
}
```
- **provider "aws"**: This block specifies that Terraform should use the AWS provider to manage infrastructure. The region is set to `us-east-1`.

#### VPC and Subnet Configuration

```hcl
# Create VPC
resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"
}
```
- **resource "aws_vpc" "main"**: This block creates a Virtual Private Cloud (VPC) with a CIDR block of `10.0.0.0/16`.

```hcl
# Create Subnets
resource "aws_subnet" "public" {
  vpc_id     = aws_vpc.main.id
  cidr_block = "10.0.1.0/24"
}

resource "aws_subnet" "private" {
  vpc_id     = aws_vpc.main.id
  cidr_block = "10.0.2.0/24"
}
```
- **resource "aws_subnet" "public"**: This block creates a public subnet within the VPC with a CIDR block of `10.0.1.0/24`.
- **resource "aws_subnet" "private"**: This block creates a private subnet within the VPC with a CIDR block of `10.0.2.0/24`.

#### Internet Gateway and NAT Gateway Configuration

```hcl
# Create Internet Gateway
resource "aws_internet_gateway" "gw" {
  vpc_id = aws_vpc.main.id
}
```
- **resource "aws_internet_gateway" "gw"**: This block creates an Internet Gateway and attaches it to the VPC, allowing internet access for instances in the public subnet.

```hcl
# Create Route Table for Public Subnet
resource "aws_route_table" "public" {
  vpc_id = aws_vpc.main.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.gw.id
  }
}

resource "aws_route_table_association" "public" {
  subnet_id      = aws_subnet.public.id
  route_table_id = aws_route_table.public.id
}
```
- **resource "aws_route_table" "public"**: This block creates a route table for the public subnet and adds a default route (0.0.0.0/0) pointing to the Internet Gateway.
- **resource "aws_route_table_association" "public"**: This block associates the route table with the public subnet.

```hcl
# Create NAT Gateway
resource "aws_eip" "nat" {
  vpc = true
}

resource "aws_nat_gateway" "nat" {
  allocation_id = aws_eip.nat.id
  subnet_id     = aws_subnet.public.id
}

resource "aws_route_table" "private" {
  vpc_id = aws_vpc.main.id

  route {
    cidr_block     = "0.0.0.0/0"
    nat_gateway_id = aws_nat_gateway.nat.id
  }
}

resource "aws_route_table_association" "private" {
  subnet_id      = aws_subnet.private.id
  route_table_id = aws_route_table.private.id
}
```
- **resource "aws_eip" "nat"**: This block creates an Elastic IP for the NAT Gateway.
- **resource "aws_nat_gateway" "nat"**: This block creates a NAT Gateway in the public subnet using the Elastic IP.
- **resource "aws_route_table" "private"**: This block creates a route table for the private subnet with a default route (0.0.0.0/0) pointing to the NAT Gateway.
- **resource "aws_route_table_association" "private"**: This block associates the route table with the private subnet.

#### Security Groups Configuration

```hcl
# Create Security Group for Bastion Host
resource "aws_security_group" "bastion" {
  vpc_id = aws_vpc.main.id

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["<your-ip-address>/32"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```
- **resource "aws_security_group" "bastion"**: This block creates a security group for the bastion host, allowing SSH access (port 22) only from your IP address and allowing all outbound traffic.

```hcl
# Create Security Group for Kubernetes Nodes
resource "aws_security_group" "kubernetes" {
  vpc_id = aws_vpc.main.id

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    security_groups = [aws_security_group.bastion.id]
  }

  ingress {
    from_port   = 6443
    to_port     = 6443
    protocol    = "tcp"
    security_groups = [aws_security_group.kubernetes.id]
  }

  ingress {
    from_port   = 10250
    to_port     = 10250
    protocol    = "tcp"
    security_groups = [aws_security_group.kubernetes.id]
  }

  ingress {
    from_port   = 10251
    to_port     = 10251
    protocol    = "tcp"
    security_groups = [aws_security_group.kubernetes.id]
  }

  ingress {
    from_port   = 10252
    to_port     = 10252
    protocol    = "tcp"
    security_groups = [aws_security_group.kubernetes.id]
  }

  ingress {
    from_port   = 10255
    to_port     = 10255
    protocol    = "tcp"
    security_groups = [aws_security_group.kubernetes.id]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```
- **resource "aws_security_group" "kubernetes"**: This block creates a security group for the Kubernetes nodes, allowing necessary communication between the bastion host and the Kubernetes control plane and worker nodes.

#### EC2 Instances Configuration

```hcl
# Create Bastion Host
resource "aws_instance" "bastion" {
  ami                    = "<ami-id>"
  instance_type          = "t2.micro"
  key_name               = "<key-pair-name>"
  subnet_id              = aws_subnet.public.id
  vpc_security_group_ids = [aws_security_group.bastion.id]

  user_data = <<-EOF
              #!/bin/bash
              yum update -y
              yum install -y aws-cli
              EOF
}
```
- **resource "aws_instance" "bastion"**: This block creates an EC2 instance in the public subnet to serve as the bastion host. The `user_data` script updates the system and installs the AWS CLI.

```hcl
# Create Control Plane Node
resource "aws_instance" "control_plane" {
  ami                    = "<ami-id>"
  instance_type          = "t2.medium"
  key_name               = "<key-pair-name>"
  subnet_id              = aws_subnet.private.id
  vpc_security_group_ids = [aws_security_group.kubernetes.id]

  user_data = <<-EOF
              #!/bin/bash
              apt-get update -y
              apt-get install -y docker.io
              systemctl start docker
              systemctl enable docker
              apt-get install -y apt-transport-https curl
              curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
              cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
              deb http://apt.kubernetes.io/ kubernetes-xenial main
              EOF
              apt-get update -y
              apt-get install -y kubelet kubeadm kubectl
              kubeadm init --pod-network-cidr=10.244.0.0/16
              mkdir -p $HOME/.kube
              cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
              chown $(id -u):$(id -g) $HOME/.kube/config
              kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
              EOF
}
```
- **resource "aws_instance" "control_plane"**: This block creates an EC2 instance in the private subnet to serve as the Kubernetes control plane node. The `user_data` script installs Docker, Kubernetes components (`kubeadm`, `kubelet`, `kubectl`), initializes the control plane, and applies the Flannel network plugin.

```hcl
# Create Data Plane Nodes
resource "aws_instance" "data_plane" {
  count                  = 2
  ami                    = "<ami-id>"
  instance_type          = "t2.medium"
  key_name               = "<key-pair-name>"
  subnet_id              = aws_subnet.private.id
  vpc_security_group_ids = [aws_security_group.kubernetes.id]

  user_data = <<-EOF
              #!/bin/bash
              apt-get

 update -y
              apt-get install -y docker.io
              systemctl start docker
              systemctl enable docker
              apt-get install -y apt-transport-https curl
              curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
              cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
              deb http://apt.kubernetes.io/ kubernetes-xenial main
              EOF
              apt-get update -y
              apt-get install -y kubelet kubeadm kubectl
              kubeadm join <control-plane-private-ip>:6443 --token <token> --discovery-token-ca-cert-hash sha256:<hash>
              EOF
}
```
- **resource "aws_instance" "data_plane"**: This block creates two EC2 instances in the private subnet to serve as the Kubernetes worker nodes. The `user_data` script installs Docker, Kubernetes components, and joins the worker nodes to the Kubernetes cluster using the control plane node's IP address, token, and discovery token CA certificate hash.

### Summary

This Terraform configuration automates the setup of an AWS infrastructure for a Kubernetes cluster running on EC2 instances. The `user_data` scripts handle the installation and configuration of Kubernetes using `kubeadm`. The configuration includes:
- Setting up a VPC, subnets, and necessary gateways.
- Creating security groups for bastion and Kubernetes nodes.
- Provisioning EC2 instances for the bastion host, Kubernetes control plane, and worker nodes.
- Initializing the Kubernetes control plane and joining worker nodes to the cluster.