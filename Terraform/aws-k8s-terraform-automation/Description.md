
### Part 1: Manual Setup of AWS VPC and Kubernetes Cluster

- **Create AWS VPC with Public & Private Subnets**
  - Create a VPC.
  - Create a public subnet.
  - Create a private subnet.

- **Use Internet Gateway with Public Subnet & NAT Gateway for Private Subnet**
  - Create and attach an Internet Gateway to the VPC.
  - Create a route table for the public subnet and associate it with the Internet Gateway.
  - Create an Elastic IP for the NAT Gateway.
  - Create a NAT Gateway and associate it with the public subnet.
  - Create a route table for the private subnet and associate it with the NAT Gateway.

- **Create Bastion Node in Public Subnet**
  - Create a security group for the bastion host with SSH access only from your local machine.
  - Launch an EC2 instance for the bastion host in the public subnet.

- **Create Security Group for Kubernetes Nodes**
  - Create a security group named `Kubernetes` for the control plane and data plane nodes.
  - Add rules to allow SSH access from the bastion security group.
  - Add rules to allow communication between Kubernetes components on required ports.

- **Create Control Plane Node in Private Subnet**
  - Launch an EC2 instance for the control plane node in the private subnet using the Kubernetes security group.

- **Create 2 Data Plane Nodes in Private Subnet**
  - Launch two EC2 instances for the data plane nodes in the private subnet using the Kubernetes security group.

- **Login into Bastion to Control Plane Node and Data Plane Nodes to Finish the Kubeadm Setup**
  - SSH into the bastion host.
  - From the bastion host, SSH into the control plane node.
  - Initialize the Kubernetes control plane node using `kubeadm`.
  - From the bastion host, SSH into each data plane node.
  - Join the data plane nodes to the Kubernetes cluster using `kubeadm`.

- **Get the Cluster Node List from Bastion**
  - SSH into the bastion host.
  - Use `kubectl` to get the list of nodes in the cluster.

### Part 2: Automate with Terraform

- **Terraform Configuration**
  - Create a Terraform configuration file (`main.tf`) to automate the setup of the AWS VPC, subnets, Internet Gateway, NAT Gateway, security groups, and EC2 instances.
  - Create a Terraform backend configuration file (`backend.tf`) to use S3 as the backend.

- **CI/CD Pipeline Configuration**
  - Create a GitHub Actions workflow file (`terraform.yml`) to automate the execution of the Terraform plan.

- **Execution Steps**
  - Push the Terraform configuration files and GitHub Actions workflow file to a GitHub repository.
  - Configure GitHub secrets for `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY`.
  - The GitHub Actions workflow will automatically run and execute the Terraform plan to set up the infrastructure.


