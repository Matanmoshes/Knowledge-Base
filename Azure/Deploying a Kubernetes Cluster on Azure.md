# **Guide: Manually Deploying a Kubernetes Cluster on Azure using Virtual Machines and `kubeadm`**

---

## **Overview**

This guide provides step-by-step instructions to manually deploy a Kubernetes cluster on Microsoft Azure using the Azure Portal (web console) and `kubeadm`. You'll set up networking components, create virtual machines for the control plane and worker nodes, and configure the cluster. At the end of each part, the equivalent Azure CLI commands are provided.

---

## **Prerequisites**

- **Azure Subscription**: An active Azure account. You can sign up for a free trial if you don't have one.
- **Basic Knowledge**: Familiarity with Azure services and Kubernetes concepts.
- **Azure CLI**: Installed on your local machine (for the optional CLI commands).

---

## **Part 1: Set Up Azure Networking via Azure Portal**

### **1. Create a Resource Group**

1. **Navigate to Resource Groups**:
   - In the Azure Portal, search for **Resource Groups** and click **Create**.

2. **Create Resource Group**:
   - **Subscription**: Select your subscription.
   - **Resource group**: `kubernetes-rg`
   - **Region**: Choose a location (e.g., **East US**).
   - Click **Review + Create**, then **Create**.

### **2. Create a Virtual Network (VNet)**

1. **Navigate to Virtual Networks**:
   - Search for **Virtual Networks** and click **Create**.

2. **Create VNet**:
   - **Subscription**: Your subscription.
   - **Resource group**: `kubernetes-rg`
   - **Name**: `kubernetes-vnet`
   - **Region**: Same as resource group.
   - **IP Addresses**:
     - **IPv4 address space**: `10.0.0.0/16`
   - **Security**: Leave default.
   - Click **Review + Create**, then **Create**.

### **3. Create Subnets**

1. **Add Subnets**:
   - After the VNet is created, navigate to it and click on **Subnets** under **Settings**.

2. **Create Public Subnet**:
   - Click **+ Subnet**.
     - **Name**: `public-subnet`
     - **Subnet address range**: `10.0.1.0/24`
     - Leave other settings default.
     - Click **Add**.

3. **Create Private Subnet**:
   - Click **+ Subnet** again.
     - **Name**: `private-subnet`
     - **Subnet address range**: `10.0.2.0/24`
     - Leave other settings default.
     - Click **Add**.

### **4. Create Network Security Groups (NSGs)**

1. **Navigate to Network Security Groups**:
   - Search for **Network Security Groups** and click **Create**.

2. **Create Bastion NSG**:
   - **Subscription**: Your subscription.
   - **Resource group**: `kubernetes-rg`
   - **Name**: `bastion-nsg`
   - **Region**: Same as resource group.
   - Click **Review + Create**, then **Create**.

3. **Create Kubernetes NSG**:
   - Repeat the steps to create another NSG:
     - **Name**: `kubernetes-nsg`
     - **Region**: Same as resource group.
     - Click **Review + Create**, then **Create**.

### **5. Configure NSG Rules**

#### **Bastion NSG Rules**

1. **Allow SSH from Your IP**:
   - Navigate to `bastion-nsg`.
   - Click on **Inbound security rules**.
   - Click **Add**.
     - **Source**: **IP Addresses**
     - **Source IP addresses/CIDR ranges**: *Your Public IP* (find it at [whatismyip.com](https://www.whatismyip.com/))
     - **Destination port ranges**: `22`
     - **Protocol**: `TCP`
     - **Action**: `Allow`
     - **Priority**: `1000`
     - **Name**: `Allow-SSH-Inbound`
     - Click **Add**.

#### **Kubernetes NSG Rules**

1. **Allow SSH from Bastion Subnet**:
   - Navigate to `kubernetes-nsg`.
   - Click on **Inbound security rules**.
   - Click **Add**.
     - **Source**: **IP Addresses**
     - **Source IP addresses/CIDR ranges**: `10.0.1.0/24` (public subnet)
     - **Destination port ranges**: `22`
     - **Protocol**: `TCP`
     - **Action**: `Allow`
     - **Priority**: `1000`
     - **Name**: `Allow-SSH-From-Bastion`
     - Click **Add**.

2. **Allow Kubernetes API Traffic**:
   - Click **Add**.
     - **Source**: **VirtualNetwork**
     - **Destination port ranges**: `6443`
     - **Protocol**: `TCP`
     - **Action**: `Allow`
     - **Priority**: `1001`
     - **Name**: `Allow-K8s-API`
     - Click **Add**.

### **6. Associate NSGs with Subnets**

1. **Associate Bastion NSG with Public Subnet**:
   - Navigate to **Virtual Networks** > `kubernetes-vnet` > **Subnets**.
   - Click on `public-subnet`.
   - Under **Network security group**, select `bastion-nsg`.
   - Click **Save**.

2. **Associate Kubernetes NSG with Private Subnet**:
   - Click on `private-subnet`.
   - Under **Network security group**, select `kubernetes-nsg`.
   - Click **Save**.

---

### **Equivalent Azure CLI Commands for Part 1**

```bash
# Create Resource Group
az group create --name kubernetes-rg --location eastus

# Create Virtual Network
az network vnet create \
  --resource-group kubernetes-rg \
  --name kubernetes-vnet \
  --address-prefixes 10.0.0.0/16

# Create Subnets
az network vnet subnet create \
  --resource-group kubernetes-rg \
  --vnet-name kubernetes-vnet \
  --name public-subnet \
  --address-prefixes 10.0.1.0/24

az network vnet subnet create \
  --resource-group kubernetes-rg \
  --vnet-name kubernetes-vnet \
  --name private-subnet \
  --address-prefixes 10.0.2.0/24

# Create NSGs
az network nsg create \
  --resource-group kubernetes-rg \
  --name bastion-nsg

az network nsg create \
  --resource-group kubernetes-rg \
  --name kubernetes-nsg

# Configure NSG Rules
az network nsg rule create \
  --resource-group kubernetes-rg \
  --nsg-name bastion-nsg \
  --name Allow-SSH-Inbound \
  --protocol Tcp \
  --priority 1000 \
  --destination-port-range 22 \
  --access Allow \
  --source-address-prefixes <Your-Public-IP> \
  --direction Inbound

az network nsg rule create \
  --resource-group kubernetes-rg \
  --nsg-name kubernetes-nsg \
  --name Allow-SSH-From-Bastion \
  --protocol Tcp \
  --priority 1000 \
  --destination-port-range 22 \
  --access Allow \
  --source-address-prefixes 10.0.1.0/24 \
  --direction Inbound

az network nsg rule create \
  --resource-group kubernetes-rg \
  --nsg-name kubernetes-nsg \
  --name Allow-K8s-API \
  --protocol Tcp \
  --priority 1001 \
  --destination-port-range 6443 \
  --access Allow \
  --source-address-prefixes 10.0.0.0/16 \
  --direction Inbound

# Associate NSGs with Subnets
az network vnet subnet update \
  --resource-group kubernetes-rg \
  --vnet-name kubernetes-vnet \
  --name public-subnet \
  --network-security-group bastion-nsg

az network vnet subnet update \
  --resource-group kubernetes-rg \
  --vnet-name kubernetes-vnet \
  --name private-subnet \
  --network-security-group kubernetes-nsg
```

---

## **Part 2: Create Virtual Machines via Azure Portal**

### **1. Create the Bastion Host**

1. **Navigate to Virtual Machines**:
   - Search for **Virtual Machines** and click **Create** > **Virtual machine**.

2. **Configure Bastion VM**:
   - **Subscription**: Your subscription.
   - **Resource group**: `kubernetes-rg`
   - **Virtual machine name**: `bastion-vm`
   - **Region**: Same as resource group.
   - **Availability options**: No infrastructure redundancy required.
   - **Image**: **Ubuntu Server 20.04 LTS**
   - **Size**: `Standard_B1s` (suitable for a bastion host).
   - **Authentication type**: **SSH public key**
     - **Username**: `azureuser`
     - **SSH public key source**: Generate new key pair or use existing.
   - **Inbound port rules**:
     - **Public inbound ports**: Allow selected ports.
     - **Select inbound ports**: **SSH (22)**
   - Click **Next: Disks**.

3. **Disks**:
   - Leave default settings.
   - Click **Next: Networking**.

4. **Networking**:
   - **Virtual network**: `kubernetes-vnet`
   - **Subnet**: `public-subnet`
   - **Public IP**: `bastion-public-ip` (automatically created)
   - **NIC network security group**: **Advanced**
     - **Configure network security group**: Leave blank (since we associated NSG at subnet level)
   - Click **Next: Management**.

5. **Management**:
   - Disable unnecessary management options to minimize costs.
   - Click **Review + Create**, then **Create**.

### **2. Create the Control Plane Node**

1. **Create VM**:
   - Click **Create** > **Virtual machine**.

2. **Configure Control Plane VM**:
   - **Resource group**: `kubernetes-rg`
   - **Virtual machine name**: `control-plane-vm`
   - **Region**: Same as resource group.
   - **Image**: **Ubuntu Server 20.04 LTS**
   - **Size**: `Standard_B2s`
   - **Authentication type**: **SSH public key**
     - **Username**: `azureuser`
     - **SSH public key source**: Use existing key pair.
   - **Inbound port rules**:
     - **Public inbound ports**: **None** (since it's in a private subnet)
   - Click **Next: Disks**.

3. **Disks**:
   - Leave default settings.
   - Click **Next: Networking**.

4. **Networking**:
   - **Virtual network**: `kubernetes-vnet`
   - **Subnet**: `private-subnet`
   - **Public IP**: **None**
   - **NIC network security group**: **Advanced**
     - **Configure network security group**: Leave blank.
   - Click **Review + Create**, then **Create**.

### **3. Create Worker Nodes**

Repeat the steps to create two more VMs:

- **Virtual machine names**: `worker-node-1` and `worker-node-2`
- **Resource group**: `kubernetes-rg`
- **Region**: Same as resource group.
- **Image**: **Ubuntu Server 20.04 LTS**
- **Size**: `Standard_B2s`
- **Authentication type**: **SSH public key**
  - **Username**: `azureuser`
  - **SSH public key source**: Use existing key pair.
- **Inbound port rules**:
  - **Public inbound ports**: **None**
- **Networking**:
  - **Virtual network**: `kubernetes-vnet`
  - **Subnet**: `private-subnet`
  - **Public IP**: **None**
  - **NIC network security group**: **Advanced**
    - **Configure network security group**: Leave blank.
- **Review + Create**, then **Create**.

### **4. Retrieve Private IP Addresses**

1. **Navigate to Each VM**:
   - Go to **Virtual Machines** and select each VM.
   - Under **Networking**, note the **Private IP address** for:
     - `control-plane-vm`
     - `worker-node-1`
     - `worker-node-2`

### **5. SSH into the Bastion Host**

1. **Obtain Bastion Public IP**:
   - Go to `bastion-vm` > **Overview**.
   - Note the **Public IP address**.

2. **SSH from Your Local Machine**:
   - Open your terminal and run:
     ```bash
     ssh -i ~/.ssh/your_private_key azureuser@<bastion-public-ip>
     ```
     - Replace `~/.ssh/your_private_key` with the path to your SSH private key.
     - Replace `<bastion-public-ip>` with the actual IP.

### **6. SSH into Private VMs from the Bastion Host**

1. **Copy SSH Private Key to Bastion Host**:
   - On your local machine:
     ```bash
     scp -i ~/.ssh/your_private_key ~/.ssh/your_private_key azureuser@<bastion-public-ip>:/home/azureuser/
     ```
   - On the bastion host, set permissions:
     ```bash
     chmod 600 your_private_key
     ```

2. **SSH into Control Plane VM**:
   ```bash
   ssh -i your_private_key azureuser@<control-plane-private-ip>
   ```

3. **SSH into Worker Nodes**:
   ```bash
   # Worker Node 1
   ssh -i your_private_key azureuser@<worker-node-1-private-ip>

   # Worker Node 2
   ssh -i your_private_key azureuser@<worker-node-2-private-ip>
   ```

---

### **Equivalent Azure CLI Commands for Part 2**

```bash
# Generate SSH Key Pair if not already done
ssh-keygen -t rsa -b 4096 -f ~/.ssh/azure_k8s_rsa -N ''

# Create Bastion VM
az vm create \
  --resource-group kubernetes-rg \
  --name bastion-vm \
  --image UbuntuLTS \
  --size Standard_B1s \
  --vnet-name kubernetes-vnet \
  --subnet public-subnet \
  --nsg "" \
  --public-ip-address bastion-public-ip \
  --admin-username azureuser \
  --ssh-key-values ~/.ssh/azure_k8s_rsa.pub

# Create Control Plane VM
az vm create \
  --resource-group kubernetes-rg \
  --name control-plane-vm \
  --image UbuntuLTS \
  --size Standard_B2s \
  --vnet-name kubernetes-vnet \
  --subnet private-subnet \
  --nsg "" \
  --public-ip-address "" \
  --admin-username azureuser \
  --ssh-key-values ~/.ssh/azure_k8s_rsa.pub

# Create Worker Nodes
az vm create \
  --resource-group kubernetes-rg \
  --name worker-node-1 \
  --image UbuntuLTS \
  --size Standard_B2s \
  --vnet-name kubernetes-vnet \
  --subnet private-subnet \
  --nsg "" \
  --public-ip-address "" \
  --admin-username azureuser \
  --ssh-key-values ~/.ssh/azure_k8s_rsa.pub

az vm create \
  --resource-group kubernetes-rg \
  --name worker-node-2 \
  --image UbuntuLTS \
  --size Standard_B2s \
  --vnet-name kubernetes-vnet \
  --subnet private-subnet \
  --nsg "" \
  --public-ip-address "" \
  --admin-username azureuser \
  --ssh-key-values ~/.ssh/azure_k8s_rsa.pub

# Get Private IP Addresses
CONTROL_PLANE_IP=$(az vm list-ip-addresses \
  --resource-group kubernetes-rg \
  --name control-plane-vm \
  --query "[].virtualMachine.network.privateIpAddresses[0]" \
  --output tsv)

WORKER_NODE_1_IP=$(az vm list-ip-addresses \
  --resource-group kubernetes-rg \
  --name worker-node-1 \
  --query "[].virtualMachine.network.privateIpAddresses[0]" \
  --output tsv)

WORKER_NODE_2_IP=$(az vm list-ip-addresses \
  --resource-group kubernetes-rg \
  --name worker-node-2 \
  --query "[].virtualMachine.network.privateIpAddresses[0]" \
  --output tsv)

# SSH into Bastion Host
ssh -i ~/.ssh/azure_k8s_rsa azureuser@<bastion-public-ip>

# From Bastion Host, SSH into Private VMs
ssh -i azure_k8s_rsa azureuser@$CONTROL_PLANE_IP
ssh -i azure_k8s_rsa azureuser@$WORKER_NODE_1_IP
ssh -i azure_k8s_rsa azureuser@$WORKER_NODE_2_IP
```

---

## **Part 3: Install Docker and Kubernetes Components**

Perform the following steps on **all nodes** (control plane and worker nodes).

### **1. Update the System**

```bash
sudo apt-get update -y && sudo apt-get upgrade -y
```

### **2. Install Docker**

```bash
sudo apt-get install -y docker.io
sudo systemctl enable docker
sudo systemctl start docker
```

### **3. Add Kubernetes Repository**

```bash
sudo apt-get install -y apt-transport-https ca-certificates curl

sudo curl -fsSL https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -

sudo bash -c 'cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF'
```

### **4. Install `kubeadm`, `kubelet`, and `kubectl`**

```bash
sudo apt-get update -y
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

### **5. Disable Swap**

```bash
sudo swapoff -a
sudo sed -i '/ swap / s/^/#/' /etc/fstab
```

---

## **Part 4: Initialize Kubernetes Cluster with `kubeadm`**

### **1. Initialize the Control Plane Node**

On the **control plane node**:

```bash
sudo kubeadm init --pod-network-cidr=10.244.0.0/16
```

- **Note**: Copy the `kubeadm join` command displayed at the end.

### **2. Set Up `kubectl` for the `azureuser`**

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

### **3. Install a Pod Network Add-on (Flannel)**

```bash
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```

---

## **Part 5: Join Worker Nodes to the Cluster**

On each **worker node**, run the `kubeadm join` command copied from the control plane node. It should look like:

```bash
sudo kubeadm join <control-plane-ip>:6443 --token <token> \
    --discovery-token-ca-cert-hash sha256:<hash>
```

Replace `<control-plane-ip>`, `<token>`, and `<hash>` with the actual values.

---

## **Part 6: Verify the Cluster**

### **1. Check Node Status from the Control Plane**

On the **control plane node**:

```bash
kubectl get nodes
```

**Expected Output**:

```
NAME               STATUS   ROLES           AGE     VERSION
control-plane-vm   Ready    control-plane   Xs      v1.XX.X
worker-node-1      Ready    <none>          Xs      v1.XX.X
worker-node-2      Ready    <none>          Xs      v1.XX.X
```

---

## **Conclusion**

Congratulations! You have successfully deployed a Kubernetes cluster on Azure using Virtual Machines and `kubeadm`. You can now deploy applications and manage your cluster using `kubectl`.

---

## **Additional Tips**

- **Scaling**: To add more worker nodes, create additional VMs and join them using the `kubeadm join` command.
- **High Availability**: For production environments, consider setting up multiple control plane nodes.
- **Cleanup**: Remember to delete resources when not in use to avoid charges.

---

## **Troubleshooting**

- **Nodes Not Ready**: Ensure that the network add-on is correctly installed and that all nodes can communicate.
- **SSH Issues**: Verify NSG rules and ensure the bastion host can access private subnets.
- **Swap Not Disabled**: Kubernetes requires swap to be disabled on all nodes.

---

## **References**

- [Azure Portal Documentation](https://docs.microsoft.com/en-us/azure/azure-portal/)
- [Kubernetes Documentation](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)
- [Flannel Networking](https://github.com/coreos/flannel)

