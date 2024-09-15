# **Guide: Manually Deploying a K8S Cluster on AWS using EC2 and `kubeadm`**

---

## **Part 1: Setup AWS Networking (VPC, Subnets, Internet Gateway, NAT Gateway)**

### **1. Create a VPC:**
   - Go to the **VPC** service in the AWS Management Console.
   - Click **Create VPC**.
     - Name: `kubernetes-vpc`
     - IPv4 CIDR block: `10.0.0.0/16`
     - Tenancy: default
   - Click **Create VPC**.

### **2. Create Subnets:**
   - Go to the **Subnets** section in the VPC dashboard.
   - Click **Create subnet** and create two subnets:
     - **Public Subnet:**
       - Name: `public-subnet`
       - VPC: `kubernetes-vpc`
       - Availability Zone: Choose `us-east-1a` or any other availability zone.
       - CIDR block: `10.0.1.0/24`
     - **Private Subnet:**
       - Name: `private-subnet`
       - VPC: `kubernetes-vpc`
       - Availability Zone: Choose `us-east-1a`.
       - CIDR block: `10.0.2.0/24`

### **3. Create an Internet Gateway:**
   - Go to the **Internet Gateways** section.
   - Click **Create internet gateway**.
     - Name: `igw-kubernetes`
   - Attach it to the `kubernetes-vpc` by selecting **Actions** -> **Attach to VPC**.

### **4. Create a NAT Gateway:**
   - Go to the **NAT Gateways** section.
   - Click **Create NAT Gateway**.
     - Subnet: `public-subnet`
     - Elastic IP: Click **Allocate Elastic IP** and select the allocated IP.

### **5. Create Route Tables:**
   - Go to the **Route Tables** section.
   - Click **Create Route Table**.
     - Name: `public-route-table`
     - VPC: `kubernetes-vpc`
   - Click **Create Route Table**.
     - Select it, then go to the **Routes** tab, and click **Edit Routes**.
     - Add a new route:  
       - Destination: `0.0.0.0/0`
       - Target: `igw-kubernetes`
   - **Associate Route Table with the Public Subnet:**
     - Select the **Associations** tab.
     - Click **Edit Subnet Associations**, and associate it with `public-subnet`.
   - Create another route table for the private subnet:
     - Name: `private-route-table`.
     - Add a new route:
       - Destination: `0.0.0.0/0`
       - Target: `NAT Gateway (nat-xxxx)`
     - Associate this route table with the `private-subnet`.

---

## **Part 2: Create EC2 Instances (Bastion, Control Plane, Data Plane)**

### **1. Create Bastion Host in the Public Subnet:**
   - Go to the **EC2** service in the AWS Management Console.
   - Click **Launch Instance**.
     - Name: `bastion`
     - AMI: Amazon Linux 2 AMI (choose the default free-tier one)
     - Instance Type: `t3.micro` (or `t2.micro` for free-tier)
     - Key Pair: Choose an existing key pair or create a new one.
     - Network Settings: 
       - VPC: `kubernetes-vpc`
       - Subnet: `public-subnet`
       - Auto-assign public IP: Enable
       - Security Group: 
         - Click **Create security group**.
         - Name: `bastion-sg`
         - Allow inbound SSH (port 22) from your local IP address.
     - Launch the instance.
   - Once launched, SSH into the bastion from your terminal:
     ```bash
     ssh -i your-key.pem ec2-user@<bastion-public-ip>
     ```

### **2. Create Control Plane Node in the Private Subnet:**
   - Go back to **Launch Instance** and create the control plane instance:
     - Name: `control-plane`
     - AMI: Amazon Linux 2 AMI
     - Instance Type: `t3.micro`
     - Key Pair: Use the same key pair.
     - Network Settings:
       - VPC: `kubernetes-vpc`
       - Subnet: `private-subnet`
       - Auto-assign public IP: Disable
       - Security Group: Create a new security group for Kubernetes nodes:
         - Name: `kubernetes-sg`
         - Allow inbound SSH (port 22) from the bastion.
         - Allow inbound on port `6443` for Kubernetes API server (from the `kubernetes-sg`).

           <img width="1392" alt="image" src="https://github.com/user-attachments/assets/13f0413e-95f1-418e-be51-1d22681d7fba">


- Launch the instance.
- Transfer the pem file to the bastion host from your local device:
       
```bash
scp -i ~/Downloads/k8s-14-09-2024.pem ~/Downloads/k8s-14-09-2024.pem ec2-user@100.24.8.132:/home/ec2-user/
```
    
 - SSH into this instance from the bastion:
     ```bash
ssh -i k8s-14-09-2024.pem ec2-user@10.0.2.197
     ```

### **3. Create Data Plane Nodes (2) in the Private Subnet:**
   - Repeat the same process for the data plane nodes:
     - Name: `data-plane-1`, `data-plane-2`
     - AMI: Amazon Linux 2 AMI
     - Instance Type: `t3.micro`
     - Key Pair: Use the same key pair.
     - Network Settings:
       - VPC: `kubernetes-vpc`
       - Subnet: `private-subnet`
       - Auto-assign public IP: Disable
       - Security Group: Use the `kubernetes-sg` created earlier.
   - SSH into these instances from the bastion:
     ```bash
     ssh -i k8s-14-09-2024.pem ec2-user@10.0.2.190
     ```

---

## **Part 3: Install Docker and Kubernetes Components**

### **1. Install Docker on All Nodes (Control Plane and Data Plane):**

   ```bash
   sudo yum update -y
   sudo yum install -y docker
   sudo systemctl enable docker
   sudo systemctl start docker
   ```

### **2. Install `kubeadm`, `kubectl`, and `kubelet` on All Nodes:**

```bash
   cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
   [kubernetes]
   name=Kubernetes
   baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-\$basearch
   enabled=1
   gpgcheck=1
   repo_gpgcheck=1
   gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
   EOF
```

- **Installing kubelet, kubeadm, kubectl:
```bash
   sudo yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes
   sudo systemctl enable kubelet && sudo systemctl start kubelet
```

---

## **Part 4: Initialize Kubernetes Cluster with `kubeadm`**

### **1. Initialize Control Plane Node:**
   On the control plane node, run:

   ```bash
   sudo kubeadm init --pod-network-cidr=10.244.0.0/16
   ```

   This command will set up the control plane. After initialization, you'll get a `kubeadm join` command with a token, which you will need to join the data plane nodes.

### **2. Configure `kubectl` on the Control Plane:**
   Set up `kubectl` so you can manage the cluster from the control plane node:

   ```bash
   mkdir -p $HOME/.kube
   sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
   sudo chown $(id -u):$(id -g) $HOME/.kube/config
   ```

#### **3. Install a Pod Network Add-on (Flannel):**

   ```bash
   kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
   ```

---

## **Part 5: Join Data Plane Nodes to the Cluster**

### **1. Join Data Plane Nodes:**
   On both data plane nodes, use the `kubeadm join` command that was generated on the control plane node:

   ```bash
   sudo kubeadm join <control-plane-ip>:6443 --token <token> \
   --discovery-token-ca-cert-hash sha256:<hash>
   ```

---

## **Part 6: Verify the Cluster**

### **1. Check Node Status from the Control Plane:**

   After joining the data plane nodes, verify that the cluster is up and running:

   ```bash
   kubectl get nodes
   ```

   You should see both the control plane and data plane nodes in the output, and their status should be `Ready`.

