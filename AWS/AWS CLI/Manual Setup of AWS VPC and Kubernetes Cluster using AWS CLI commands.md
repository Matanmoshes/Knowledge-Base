# **Guide: Deploying an AWS VPC and Kubernetes Cluster using AWS CLI**

---

#### **Overview**

This guide provides detailed steps to manually create an AWS Virtual Private Cloud (VPC) with public and private subnets, set up a Kubernetes cluster, and configure necessary components using AWS CLI commands. The setup will include a bastion host for secure access and a Kubernetes cluster running in the private subnet.

#### **Infrastructure Diagram**

The following diagram outlines the structure of the resources you'll create on AWS:

```mathematica
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

## **Step-by-Step Guide**

---

#### **1. Create an AWS VPC with Public and Private Subnets**

**Step 1: Create a VPC**

```bash
aws ec2 create-vpc --cidr-block 10.0.0.0/16
```

- This command creates a new VPC with the specified CIDR block. Note the VPC ID from the output for future use.

**Step 2: Create Public and Private Subnets**

```bash
# Get the VPC ID for a specific CIDR block
aws ec2 describe-vpcs --filters "Name=cidr,Values=10.0.0.0/16" --query "Vpcs[0].VpcId" --output text

# Public Subnet
aws ec2 create-subnet --vpc-id <vpc-id> --cidr-block 10.0.1.0/24 --availability-zone us-east-1a

# Private Subnet
aws ec2 create-subnet --vpc-id <vpc-id> --cidr-block 10.0.2.0/24 --availability-zone us-east-1a
```

- The public subnet will host the bastion host, and the private subnet will be used for the Kubernetes control plane and worker nodes.

---

#### **2. Configure Internet Gateway and NAT Gateway**

**Step 1: Create and Attach an Internet Gateway**

```bash
aws ec2 create-internet-gateway
aws ec2 attach-internet-gateway --vpc-id <vpc-id> --internet-gateway-id <internet-gateway-id>
```

- The Internet Gateway enables communication between the VPC and the internet, primarily for the public subnet.

**Step 2: Create a Route Table for the Public Subnet**

```bash
aws ec2 create-route-table --vpc-id <vpc-id>
aws ec2 create-route --route-table-id <route-table-id> --destination-cidr-block 0.0.0.0/0 --gateway-id <internet-gateway-id>
aws ec2 associate-route-table --subnet-id <public-subnet-id> --route-table-id <route-table-id>
```

- This route table ensures that traffic from the public subnet is directed to the Internet Gateway.

**Step 3: Create a NAT Gateway for the Private Subnet**

```bash
aws ec2 allocate-address --domain vpc
aws ec2 create-nat-gateway --subnet-id <public-subnet-id> --allocation-id <elastic-ip-id>
```

- The NAT Gateway allows instances in the private subnet to initiate outbound internet traffic without exposing them directly to the internet.

**Step 4: Create a Route Table for the Private Subnet**

```bash
aws ec2 create-route-table --vpc-id <vpc-id>
aws ec2 create-route --route-table-id <private-route-table-id> --destination-cidr-block 0.0.0.0/0 --nat-gateway-id <nat-gateway-id>
aws ec2 associate-route-table --subnet-id <private-subnet-id> --route-table-id <private-route-table-id>
```

- This route table directs outbound internet traffic from the private subnet through the NAT Gateway.

---

#### **3. Create a Bastion Host in the Public Subnet**

**Step 1: Create a Security Group for the Bastion Host**

```bash
aws ec2 create-security-group --group-name bastion-sg --description "Bastion security group" --vpc-id <vpc-id>
aws ec2 authorize-security-group-ingress --group-id <bastion-sg-id> --protocol tcp --port 22 --cidr <your-ip-address>/32
```

- This security group allows SSH access to the bastion host from your IP address.

**Step 2: Launch an EC2 Instance for the Bastion Host**

```bash
aws ec2 run-instances --image-id <ami-id> --count 1 --instance-type t2.micro --key-name <key-pair-name> --security-group-ids <bastion-sg-id> --subnet-id <public-subnet-id>
```

- The bastion host will serve as a secure access point to the instances within the private subnet.

---

#### **4. Create Security Groups for Kubernetes Nodes**

**Step 1: Create a Security Group for Kubernetes Nodes**

```bash
aws ec2 create-security-group --group-name kubernetes-sg --description "Kubernetes security group" --vpc-id <vpc-id>

aws ec2 authorize-security-group-ingress --group-id <kubernetes-sg-id> --protocol tcp --port 22 --source-group <bastion-sg-id>
aws ec2 authorize-security-group-ingress --group-id <kubernetes-sg-id> --protocol tcp --port 6443 --source-group <kubernetes-sg-id>
aws ec2 authorize-security-group-ingress --group-id <kubernetes-sg-id> --protocol tcp --port 10250 --source-group <kubernetes-sg-id>
aws ec2 authorize-security-group-ingress --group-id <kubernetes-sg-id> --protocol tcp --port 10251 --source-group <kubernetes-sg-id>
aws ec2 authorize-security-group-ingress --group-id <kubernetes-sg-id> --protocol tcp --port 10252 --source-group <kubernetes-sg-id>
aws ec2 authorize-security-group-ingress --group-id <kubernetes-sg-id> --protocol tcp --port 10255 --source-group <kubernetes-sg-id>
```

- These rules allow necessary communication between the bastion host and Kubernetes nodes and between the Kubernetes nodes themselves.

---

#### **5. Create Control Plane and Data Plane Nodes in the Private Subnet**

**Step 1: Launch an EC2 Instance for the Control Plane Node**

```bash
aws ec2 run-instances --image-id <ami-id> --count 1 --instance-type t2.medium --key-name <key-pair-name> --security-group-ids <kubernetes-sg-id> --subnet-id <private-subnet-id>
```

- The control plane node manages the Kubernetes cluster.

**Step 2: Launch EC2 Instances for the Data Plane Nodes**

```bash
aws ec2 run-instances --image-id <ami-id> --count 2 --instance-type t2.medium --key-name <key-pair-name> --security-group-ids <kubernetes-sg-id> --subnet-id <private-subnet-id>
```

- These are the worker nodes that will run the Kubernetes workloads.

---

#### **6. Complete Kubeadm Setup**

**Step 1: SSH into Bastion and Control Plane Node**

```bash
# SSH into bastion
ssh -i <key-pair-name>.pem ec2-user@<bastion-public-ip>

# From the bastion, SSH into the control plane node
ssh -i <key-pair-name>.pem ec2-user@<control-plane-private-ip>
```

**Step 2: Initialize the Control Plane Node**

```bash
sudo kubeadm init --pod-network-cidr=10.244.0.0/16
```

- This command initializes the Kubernetes control plane.

**Step 3: Join the Data Plane Nodes to the Cluster**

```bash
# From the bastion, SSH into the data plane nodes
ssh -i <key-pair-name>.pem ec2-user@<data-plane-node1-private-ip>
ssh -i <key-pair-name>.pem ec2-user@<data-plane-node2-private-ip>

sudo kubeadm join <control-plane-private-ip>:6443 --token <token> --discovery-token-ca-cert-hash sha256:<hash>
```

- This command connects your worker nodes to the Kubernetes control plane.

---

#### **7. Get the Cluster Node List from Bastion**

**Step 1: SSH into Bastion**

```bash
ssh -i <key-pair-name>.pem ec2-user@<bastion-public-ip>
```

**Step 2: Get the Cluster Node List**

```bash
kubectl get nodes
```

- Use this command to verify that all nodes are connected and ready.

