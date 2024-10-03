# **Comprehensive Guide: Manually Deploying a Kubernetes Cluster on Azure using Virtual Machines and `kubeadm`**

---

## **Overview**

This guide provides a **comprehensive, step-by-step** process to manually deploy a Kubernetes cluster on Microsoft Azure using **Virtual Machines (VMs)** and **`kubeadm`**. The deployment includes:

- **Setting Up Networking**: Creating virtual networks, subnets, and network security groups (NSGs).
- **Creating Virtual Machines**: Setting up a Bastion Host, Control Plane, and Worker Nodes.
- **Configuring Secure Access**: Using the Bastion Host for secure SSH access to private VMs.
- **Installing Required Software**: Installing Docker and Kubernetes components on all nodes.
- **Initializing the Kubernetes Cluster**: Setting up the Control Plane and joining Worker Nodes.
- **Verifying the Cluster**: Ensuring all components are functioning correctly.

By the end of this guide, you'll have a functional Kubernetes cluster deployed on Azure, ready to run containerized applications.

---

## **Prerequisites**

Before beginning, ensure you have the following:

1. **Azure Subscription**: An active Azure account. If you don't have one, [sign up for free](https://azure.microsoft.com/free/).
2. **Azure CLI**: Installed on your local machine for CLI-based operations. [Install Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).
3. **Basic Knowledge**: Familiarity with Azure services and Kubernetes concepts.
4. **SSH Key Pair**: Generated for secure access to VMs. [Generate SSH Keys](#generate-ssh-key-pair).

---

## **Generate SSH Key Pair**

SSH keys are essential for secure access to your Azure VMs. If you haven't already generated an SSH key pair, follow these steps.

### **Steps to Generate an SSH Key Pair**

1. **Open Terminal or Command Prompt** on your local machine.

2. **Run the SSH Key Generation Command:**

   ```bash
   ssh-keygen -t rsa -b 4096 -f ~/.ssh/azure_k8s_rsa -N ''
   ```

   - **Explanation:**
     - `-t rsa`: Specifies the type of key to create (RSA).
     - `-b 4096`: Generates a 4096-bit key for enhanced security.
     - `-f ~/.ssh/azure_k8s_rsa`: Specifies the file path for the key.
     - `-N ''`: Sets an empty passphrase (you can set a passphrase if desired).

3. **Verify Key Generation:**

   ```bash
   ls ~/.ssh/azure_k8s_rsa*
   ```

   - **Expected Output:**
     - `azure_k8s_rsa`
     - `azure_k8s_rsa.pub`

4. **Secure Your SSH Keys:**

   Ensure your private key (`azure_k8s_rsa`) is stored securely and **never** share it.

---

## **Set Up Azure Networking**

Proper networking setup is crucial for Kubernetes cluster communication and security. This involves creating a **Virtual Network (VNet)** with appropriate **subnets**.

### **4.1 Create a Resource Group**

A **Resource Group** is a container that holds related resources for an Azure solution.

#### **Steps to Create a Resource Group:**

1. **Navigate to the Azure Portal:**

   Open your browser and go to [Azure Portal](https://portal.azure.com/).

2. **Search for Resource Groups:**

   - Use the search bar at the top to search for **"Resource Groups"** and select it.

3. **Create a New Resource Group:**

   - Click on **"Create"**.

4. **Configure the Resource Group:**

   | **Field**        | **Value to Set**        |
   |------------------|-------------------------|
   | **Subscription** | *Your Azure Subscription* |
   | **Resource group** | `kubernetes-rg`       |
   | **Region**       | Choose a region (e.g., East US) |

5. **Review and Create:**

   - Click **"Review + create"**.
   - After validation, click **"Create"**.

---

### **4.2 Create a Virtual Network and Subnets**

A **Virtual Network (VNet)** is a representation of your own network in the cloud. It is a logical isolation of the Azure cloud dedicated to your subscription.

#### **Steps to Create a Virtual Network and Subnets:**

1. **Navigate to Virtual Networks:**

   - In the Azure Portal, use the search bar to find **"Virtual Networks"** and select it.

2. **Create a New Virtual Network:**

   - Click on **"Create"**.

3. **Configure the Virtual Network:**

   | **Field**            | **Value to Set**                                  |
   |----------------------|---------------------------------------------------|
   | **Subscription**     | *kubernetes-rg's subscription*                    |
   | **Resource group**   | `kubernetes-rg`                                   |
   | **Name**             | `kubernetes-vnet`                                 |
   | **Region**           | Same as the resource group (e.g., East US)        |

4. **Configure IP Addresses:**

   - **IPv4 Address Space:**

     | **Field**          | **Value to Set**      |
     |--------------------|-----------------------|
     | **Address space**  | `10.0.0.0/16`         |

   - **Subnets:**
     
     - **Public Subnet:**
     
       | **Subnet Name**  | **Address Range** |
       |------------------|--------------------|
       | `public-subnet`  | `10.0.1.0/24`      |
     
     - **Private Subnet:**
     
       | **Subnet Name**   | **Address Range** |
       |-------------------|--------------------|
       | `private-subnet`  | `10.0.2.0/24`      |

5. **Review and Create:**

   - Click **"Review + create"**.
   - After validation, click **"Create"**.

---

## **Set Up Network Security Groups (NSGs)**

**Network Security Groups (NSGs)** contain a list of security rules that allow or deny network traffic to resources connected to Azure VNets.

### **5.1 Create Bastion NSG**

The **Bastion NSG** controls inbound traffic to the Bastion Host.

#### **Steps to Create Bastion NSG:**

1. **Navigate to Network Security Groups:**

   - In the Azure Portal, search for **"Network Security Groups"** and select it.

2. **Create a New NSG:**

   - Click on **"Create"**.

3. **Configure the NSG:**

   | **Field**            | **Value to Set**        |
   |----------------------|-------------------------|
   | **Subscription**     | *kubernetes-rg's subscription* |
   | **Resource group**   | `kubernetes-rg`         |
   | **Name**             | `bastion-nsg`           |
   | **Region**           | Same as the resource group (e.g., East US) |

4. **Review and Create:**

   - Click **"Review + create"**.
   - After validation, click **"Create"**.

---

### **5.2 Create Kubernetes NSG**

The **Kubernetes NSG** controls inbound traffic to the Kubernetes cluster (Control Plane and Worker Nodes).

#### **Steps to Create Kubernetes NSG:**

1. **Navigate to Network Security Groups:**

   - In the Azure Portal, search for **"Network Security Groups"** and select it.

2. **Create a New NSG:**

   - Click on **"Create"**.

3. **Configure the NSG:**

   | **Field**            | **Value to Set**        |
   |----------------------|-------------------------|
   | **Subscription**     | *kubernetes-rg's subscription* |
   | **Resource group**   | `kubernetes-rg`         |
   | **Name**             | `kubernetes-nsg`        |
   | **Region**           | Same as the resource group (e.g., East US) |

4. **Review and Create:**

   - Click **"Review + create"**.
   - After validation, click **"Create"**.

---

### **5.3 Configure NSG Rules**

Properly configuring NSG rules ensures secure and functional communication between your cluster components.

#### **NSG Rules Overview**

1. **Bastion NSG (`bastion-nsg`):**
   - **Allow SSH Access from Your Local Machine**: Enables you to SSH into the Bastion Host securely.

2. **Kubernetes NSG (`kubernetes-nsg`):**
   - **Allow SSH Access from Bastion Subnet to Kubernetes Nodes**: Permits SSH connections from the Bastion Host to Control Plane and Worker Nodes.
   - **Allow Kubernetes API Traffic within the VNet**: Ensures that Kubernetes API server can communicate within the VNet.

#### **Steps to Configure NSG Rules:**

##### **A. Configure Bastion NSG (`bastion-nsg`)**

**Objective:** Allow your local machine to SSH into the Bastion Host.

1. **Navigate to `bastion-nsg`:**

   - In the Azure Portal, go to **Resource Groups** > `kubernetes-rg` > **Network Security Groups** > `bastion-nsg`.

2. **Add an Inbound Security Rule:**

   - Click on **"Inbound security rules"**.
   - Click **"+ Add"** to create a new rule.

3. **Configure the Rule:**

   | **Field**                           | **Value to Set**                                                                                  |
   |-------------------------------------|---------------------------------------------------------------------------------------------------|
   | **Source**                          | **IP Addresses**                                                                                   |
   | **Source IP addresses/CIDR ranges** | *Your Public IP* (find it at [whatismyip.com](https://www.whatismyip.com/))                       |
   | **Source port ranges**              | `*` (Any)                                                                                          |
   | **Destination**                     | **Any**                                                                                            |
   | **Destination port ranges**         | `22` (SSH)                                                                                          |
   | **Protocol**                        | **TCP**                                                                                            |
   | **Action**                          | **Allow**                                                                                          |
   | **Priority**                        | `1000`                                                                                             |
   | **Name**                            | `Allow-SSH-Inbound`                                                                                |
   | **Description**                     | *(Optional)* "Allow SSH access from my IP to Bastion Host"                                        |

4. **Save the Rule:**

   - After filling in the details, click **"Add"** to create the rule.

##### **B. Configure Kubernetes NSG (`kubernetes-nsg`)**

**Objective 1:** Allow SSH access from the Bastion Host to Kubernetes Nodes.

**Objective 2:** Allow Kubernetes API server traffic within the VNet.

---

**1. Allow SSH from Bastion Subnet to Kubernetes Nodes**

- **Steps:**

  1. **Navigate to `kubernetes-nsg`:**

     - In the Azure Portal, go to **Resource Groups** > `kubernetes-rg` > **Network Security Groups** > `kubernetes-nsg`.

  2. **Add an Inbound Security Rule:**

     - Click on **"Inbound security rules"**.
     - Click **"+ Add"** to create a new rule.

  3. **Configure the Rule:**

     | **Field**                           | **Value to Set**                                                                         |
     |-------------------------------------|------------------------------------------------------------------------------------------|
     | **Source**                          | **IP Addresses**                                                                         |
     | **Source IP addresses/CIDR ranges** | `10.0.1.0/24` (Public Subnet where Bastion Host resides)                                |
     | **Source port ranges**              | `*` (Any)                                                                                 |
     | **Destination**                     | **IP Addresses**                                                                         |
     | **Destination IP addresses/CIDR ranges** | `10.0.2.0/24` (Private Subnet where Kubernetes Nodes reside)                           |
     | **Service**                         | **Custom**                                                                                |
     | **Destination port ranges**         | `22` (SSH)                                                                                 |
     | **Protocol**                        | **TCP**                                                                                   |
     | **Action**                          | **Allow**                                                                                 |
     | **Priority**                        | `100`                                                                                     |
     | **Name**                            | `Allow-SSH-From-Bastion`                                                                  |
     | **Description**                     | *(Optional)* "Allow SSH from Bastion Subnet to Kubernetes Nodes"                         |

  4. **Save the Rule:**

     - Click **"Add"** to create the rule.

---

**2. Allow Kubernetes API Traffic within the VNet**

- **Steps:**

  1. **Still in `kubernetes-nsg`:**

     - Click on **"Inbound security rules"**.
     - Click **"+ Add"** to create another new rule.

  2. **Configure the Rule:**

     | **Field**                           | **Value to Set**                                                         |
     |-------------------------------------|--------------------------------------------------------------------------|
     | **Source**                          | **Service Tag**                                                         |
     | **Source Service Tag**              | **VirtualNetwork**                                                      |
     | **Source port ranges**              | `*` (Any)                                                                |
     | **Destination**                     | **Any**                                                                  |
     | **Destination port ranges**         | `6443` (Kubernetes API Server)                                          |
     | **Protocol**                        | **TCP**                                                                  |
     | **Action**                          | **Allow**                                                                |
     | **Priority**                        | `110`                                                                    |
     | **Name**                            | `Allow-K8s-API`                                                          |
     | **Description**                     | *(Optional)* "Allow inbound Kubernetes API traffic from VNet"            |

     > **Note:** If **"VirtualNetwork"** is not available as a **Service Tag**, use the VNet's CIDR block (`10.0.0.0/16`) instead for the **Source IP addresses/CIDR ranges**.

  3. **Save the Rule:**

     - Click **"Add"** to create the rule.

---

**Summary of NSG Rules**

| **NSG Name**      | **Rule Name**            | **Source**        | **Source IP/CIDR**   | **Source Port** | **Destination** | **Destination Port** | **Protocol** | **Action** | **Priority** |
|-------------------|--------------------------|-------------------|----------------------|------------------|-----------------|----------------------|--------------|------------|--------------|
| `bastion-nsg`     | `Allow-SSH-Inbound`      | IP Addresses      | *Your Public IP*     | `*`              | Any             | `22`                 | TCP          | Allow      | `1000`       |
| `kubernetes-nsg`  | `Allow-SSH-From-Bastion`| IP Addresses      | `10.0.1.0/24`        | `*`              | IP Addresses    | `22`                 | TCP          | Allow      | `100`        |
| `kubernetes-nsg`  | `Allow-K8s-API`          | Service Tag       | `VirtualNetwork` or `10.0.0.0/16` | `*` | Any | `6443` | TCP | Allow | `110` |

---

## **Create Virtual Machines**

With networking and security configurations in place, proceed to create the necessary VMs: **Bastion Host**, **Control Plane Node**, and **Worker Nodes**.

### **6.1 Create the Bastion Host**

The **Bastion Host** serves as a secure gateway to access VMs in the private subnet.

#### **Why Use a Bastion Host?**

- **Security**: Eliminates the need to expose VMs directly to the internet.
- **Access Control**: Centralizes SSH access through a single, secure point.
- **Cost-Effective**: Avoids the need for individual public IPs for each VM.

#### **Step-by-Step Guide to Create the Bastion Host:**

1. **Navigate to Virtual Machines:**

   - In the Azure Portal, use the search bar to find and select **"Virtual Machines"**.

2. **Initiate VM Creation:**

   - Click on **"Create"** > **"Virtual machine"**.

3. **Configure the Basic Settings:**

   | **Field**            | **Value to Set**                                  |
   |----------------------|---------------------------------------------------|
   | **Subscription**     | *Your Azure Subscription*                         |
   | **Resource group**   | `kubernetes-rg`                                   |
   | **Virtual machine name** | `bastion-vm`                                 |
   | **Region**           | Same as the resource group (e.g., East US)        |
   | **Availability options** | **No infrastructure redundancy required**     |
   | **Image**            | **Ubuntu Server 20.04 LTS**                       |
   | **Size**             | `Standard_B1s` (suitable for a Bastion Host)       |
   | **Authentication type** | **SSH public key**                             |
   | **Username**         | `azureuser`                                       |
   | **SSH public key source** | **Generate new key pair** or **Use existing key** |
   | **Inbound port rules** | **Allow selected ports**                        |
   | **Select inbound ports** | **SSH (22)**                                  |

   > **Note:** Choosing **Ubuntu Server 20.04 LTS** ensures stability and compatibility. *(Refer to [Why Ubuntu Server 20.04 LTS?](#why-ubuntu-server-2004-lts))*.

4. **Configure Disks:**

   - **OS Disk Type:** **Standard SSD** (default).
   - **Encryption:** **Default (Encryption At-Rest)**.
   - **Data Disks:** **None**.

5. **Configure Networking:**

   | **Field**                      | **Value to Set**                        |
   |--------------------------------|-----------------------------------------|
   | **Virtual network**            | `kubernetes-vnet`                       |
   | **Subnet**                     | `public-subnet`                         |
   | **Public IP**                  | `bastion-public-ip` (automatically created) |
   | **NIC network security group** | **Advanced**                            |
   | **Configure network security group** | **Leave blank** (NSG associated at subnet level) |

6. **Configure Management:**

   | **Field**                    | **Value to Set**                                               |
   |------------------------------|----------------------------------------------------------------|
   | **Monitoring**               | **Disable Boot Diagnostics and other unnecessary options**      |
   | **Identity**                 | **System assigned** (default)                                 |
   | **Backup**                   | **Off**                                                        |
   | **Advanced Options**         | **Leave default or adjust based on requirements**             |
   | **Tags**                     | *(Optional)* Add tags for resource management                 |

   > **Explanation:** Disabling unnecessary management options minimizes costs.

7. **Review and Create:**

   - Click **"Review + create"**.
   - After validation, click **"Create"**.
   - **Deployment Time:** A few minutes. Monitor the deployment status in the Azure Portal.

---

### **6.2 Create the Control Plane Node**

The **Control Plane Node** manages the Kubernetes cluster, handling API requests, scheduling, and maintaining cluster state.

#### **Step-by-Step Guide to Create the Control Plane Node:**

1. **Navigate to Virtual Machines:**

   - In the Azure Portal, search for **"Virtual Machines"** and select it.

2. **Initiate VM Creation:**

   - Click on **"Create"** > **"Virtual machine"**.

3. **Configure the Basic Settings:**

   | **Field**            | **Value to Set**                                  |
   |----------------------|---------------------------------------------------|
   | **Subscription**     | *Your Azure Subscription*                         |
   | **Resource group**   | `kubernetes-rg`                                   |
   | **Virtual machine name** | `control-plane-vm`                           |
   | **Region**           | Same as the resource group (e.g., East US)        |
   | **Availability options** | **No infrastructure redundancy required**     |
   | **Image**            | **Ubuntu Server 20.04 LTS**                       |
   | **Size**             | `Standard_B2s` (suitable for Control Plane)        |
   | **Authentication type** | **SSH public key**                             |
   | **Username**         | `azureuser`                                       |
   | **SSH public key source** | **Use existing key pair**                    |
   | **Inbound port rules** | **None** (since it's in a private subnet)        |
   | **Select inbound ports** | *Not applicable*                              |

4. **Configure Disks:**

   - **OS Disk Type:** **Standard SSD** (default).
   - **Encryption:** **Default (Encryption At-Rest)**.
   - **Data Disks:** **None**.

5. **Configure Networking:**

   | **Field**                      | **Value to Set**                        |
   |--------------------------------|-----------------------------------------|
   | **Virtual network**            | `kubernetes-vnet`                       |
   | **Subnet**                     | `private-subnet`                        |
   | **Public IP**                  | **None**                                |
   | **NIC network security group** | **Advanced**                            |
   | **Configure network security group** | **Leave blank** (NSG associated at subnet level) |

6. **Configure Management:**

   | **Field**                    | **Value to Set**                                               |
   |------------------------------|----------------------------------------------------------------|
   | **Monitoring**               | **Disable Boot Diagnostics and other unnecessary options**      |
   | **Identity**                 | **System assigned** (default)                                 |
   | **Backup**                   | **Off**                                                        |
   | **Advanced Options**         | **Leave default or adjust based on requirements**             |
   | **Tags**                     | *(Optional)* Add tags for resource management                 |

   > **Explanation:** Disabling unnecessary management options minimizes costs.

7. **Review and Create:**

   - Click **"Review + create"**.
   - After validation, click **"Create"**.
   - **Deployment Time:** A few minutes. Monitor the deployment status in the Azure Portal.

---

### **6.3 Create Worker Nodes**

**Worker Nodes** are the machines that run your containerized applications. Deploying multiple Worker Nodes ensures high availability and scalability.

#### **Step-by-Step Guide to Create Worker Nodes:**

> **Note:** Repeat these steps for each Worker Node (`worker-node-1` and `worker-node-2`).

1. **Navigate to Virtual Machines:**

   - In the Azure Portal, search for **"Virtual Machines"** and select it.

2. **Initiate VM Creation:**

   - Click on **"Create"** > **"Virtual machine"**.

3. **Configure the Basic Settings:**

   | **Field**            | **Value to Set**                                  |
   |----------------------|---------------------------------------------------|
   | **Subscription**     | *Your Azure Subscription*                         |
   | **Resource group**   | `kubernetes-rg`                                   |
   | **Virtual machine name** | `worker-node-1` or `worker-node-2`           |
   | **Region**           | Same as the resource group (e.g., East US)        |
   | **Availability options** | **No infrastructure redundancy required**     |
   | **Image**            | **Ubuntu Server 20.04 LTS**                       |
   | **Size**             | `Standard_B2s` (suitable for Worker Nodes)         |
   | **Authentication type** | **SSH public key**                             |
   | **Username**         | `azureuser`                                       |
   | **SSH public key source** | **Use existing key pair**                    |
   | **Inbound port rules** | **None** (since it's in a private subnet)        |
   | **Select inbound ports** | *Not applicable*                              |

4. **Configure Disks:**

   - **OS Disk Type:** **Standard SSD** (default).
   - **Encryption:** **Default (Encryption At-Rest)**.
   - **Data Disks:** **None**.

5. **Configure Networking:**

   | **Field**                      | **Value to Set**                        |
   |--------------------------------|-----------------------------------------|
   | **Virtual network**            | `kubernetes-vnet`                       |
   | **Subnet**                     | `private-subnet`                        |
   | **Public IP**                  | **None**                                |
   | **NIC network security group** | **Advanced**                            |
   | **Configure network security group** | **Leave blank** (NSG associated at subnet level) |

6. **Configure Management:**

   | **Field**                    | **Value to Set**                                               |
   |------------------------------|----------------------------------------------------------------|
   | **Monitoring**               | **Disable Boot Diagnostics and other unnecessary options**      |
   | **Identity**                 | **System assigned** (default)                                 |
   | **Backup**                   | **Off**                                                        |
   | **Advanced Options**         | **Leave default or adjust based on requirements**             |
   | **Tags**                     | *(Optional)* Add tags for resource management                 |

   > **Explanation:** Disabling unnecessary management options minimizes costs.

7. **Review and Create:**

   - Click **"Review + create"**.
   - After validation, click **"Create"**.
   - **Deployment Time:** A few minutes. Monitor the deployment status in the Azure Portal.

---

## **Retrieve Private IP Addresses**

Private IP addresses are essential for internal communication within the Kubernetes cluster and for secure SSH access via the Bastion Host.

### **Steps to Retrieve Private IP Addresses:**

1. **Navigate to Virtual Machines:**

   - In the Azure Portal, search for **"Virtual Machines"** and select it.

2. **Select Each VM:**

   - Click on each VM you created (`control-plane-vm`, `worker-node-1`, `worker-node-2`, `bastion-vm`) one by one.

3. **View Networking Details:**

   - In the VM's overview pane, find and click on **"Networking"** under the **"Settings"** section.

4. **Note the Private IP Address:**

   - Under the **"Network interface"** section, locate the **"Private IP address"** field.
   - **Example:**
     - `control-plane-vm`: `10.0.2.4`
     - `worker-node-1`: `10.0.2.5`
     - `worker-node-2`: `10.0.2.6`
     - `bastion-vm`: `10.0.1.4`

5. **Record the IP Addresses:**

   - It's helpful to maintain a record of these IPs for future reference and configuration.

---

## **Configure SSH Access via Bastion Host**

Since the **Control Plane** and **Worker Nodes** are in a **private subnet**, direct SSH access from the internet is not possible. The **Bastion Host** serves as a secure gateway to access these private VMs.

### **8.1 SSH into the Bastion Host**

#### **Steps to SSH into the Bastion Host:**

1. **Obtain Bastion Host Public IP Address:**

   - In the Azure Portal, navigate to **Virtual Machines** > `bastion-vm`.
   - Under **"Overview"**, note the **"Public IP address"**.

2. **SSH from Your Local Machine:**

   Open your terminal and execute:

   ```bash
   ssh -i ~/.ssh/azure_k8s_rsa azureuser@<bastion-public-ip>
   ```

   - **Replace `<bastion-public-ip>`** with the actual public IP of your Bastion Host.
   - **Example:**

     ```bash
     ssh -i ~/.ssh/azure_k8s_rsa azureuser@52.170.45.123
     ```

   - **Note:** Ensure your local machine's IP is allowed in the Bastion NSG (`Allow-SSH-Inbound` rule).

3. **Verify SSH Connection:**

   - Upon successful connection, you should see a welcome message from the Ubuntu server.

---

### **8.2 SSH into Control Plane and Worker Nodes**

With SSH access to the Bastion Host established, you can now SSH into the private VMs.

#### **Steps to SSH into Private VMs from the Bastion Host:**

1. **Copy SSH Private Key to Bastion Host:**

   Since the private VMs don't have public IPs, you'll need to use the Bastion Host as a jump server.

   - **From Your Local Machine:**

     ```bash
     scp -i ~/.ssh/azure_k8s_rsa ~/.ssh/azure_k8s_rsa azureuser@<bastion-public-ip>:/home/azureuser/
     ```

     - **Explanation:**
       - `scp`: Secure copy command.
       - `-i ~/.ssh/azure_k8s_rsa`: Specifies the SSH private key.
       - `~/.ssh/azure_k8s_rsa`: Path to your SSH private key.
       - `azureuser@<bastion-public-ip>:/home/azureuser/`: Destination path on the Bastion Host.

   - **On the Bastion Host:**

     ```bash
     chmod 600 ~/azure_k8s_rsa
     ```

     - **Explanation:** Sets appropriate permissions for the SSH key.

2. **SSH into the Control Plane VM:**

   ```bash
   ssh -i ~/azure_k8s_rsa azureuser@10.0.2.4
   ```

   - **Replace `10.0.2.4`** with the actual private IP of your Control Plane VM.

3. **SSH into Worker Nodes:**

   - **Worker Node 1:**

     ```bash
     ssh -i ~/azure_k8s_rsa azureuser@10.0.2.5
     ```

   - **Worker Node 2:**

     ```bash
     ssh -i ~/azure_k8s_rsa azureuser@10.0.2.6
     ```

   - **Replace the IPs with the actual private IPs of your Worker Nodes.

4. **Confirm Successful SSH Access:**

   - Upon successful connection, you should see a welcome message from each Ubuntu server.

---

## **Install Docker and Kubernetes Components**

With access to all VMs established, proceed to install **Docker** and **Kubernetes components** on each node (Control Plane and Worker Nodes).

### **9.1 Install Docker**

Docker is a prerequisite for Kubernetes, serving as the container runtime.

#### **Steps to Install Docker on All Nodes:**

1. **SSH into Each VM:**

   - Use the Bastion Host to SSH into each node (`control-plane-vm`, `worker-node-1`, `worker-node-2`).

2. **Update the System:**

   ```bash
   sudo apt-get update -y && sudo apt-get upgrade -y
   ```

3. **Install Docker:**

   ```bash
   sudo apt-get install -y docker.io
   ```

4. **Enable and Start Docker:**

   ```bash
   sudo systemctl enable docker
   sudo systemctl start docker
   ```

5. **Add Your User to the Docker Group (Optional but Recommended):**

   ```bash
   sudo usermod -aG docker $USER
   ```

   - **Note:** After running this command, **log out and log back in** to apply the group changes.

6. **Verify Docker Installation:**

   ```bash
   docker --version
   ```

   - **Expected Output:**
     ```
     Docker version 20.10.12, build e91ed57
     ```

---

### **9.2 Install Kubernetes (`kubeadm`, `kubectl`, `kubelet`)**

These components are essential for initializing and managing the Kubernetes cluster.

#### **Steps to Install Kubernetes Components on All Nodes:**

1. **SSH into Each VM:**

   - Use the Bastion Host to SSH into each node.

2. **Install Required Packages:**

   ```bash
   sudo apt-get install -y apt-transport-https ca-certificates curl
   ```

3. **Add Kubernetes GPG Key:**

   ```bash
   curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
   ```

4. **Add Kubernetes Repository:**

   ```bash
   sudo bash -c 'cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
   deb https://apt.kubernetes.io/ kubernetes-xenial main
   EOF'
   ```

5. **Update Package List:**

   ```bash
   sudo apt-get update -y
   ```

6. **Install `kubeadm`, `kubectl`, and `kubelet`:**

   ```bash
   sudo apt-get install -y kubelet kubeadm kubectl
   sudo apt-mark hold kubelet kubeadm kubectl
   ```

   - **Explanation:**
     - `kubelet`: Runs on all nodes and manages pod lifecycle.
     - `kubeadm`: Initializes the cluster.
     - `kubectl`: Command-line tool to interact with the cluster.
     - `apt-mark hold`: Prevents these packages from being automatically updated.

7. **Verify Kubernetes Components Installation:**

   ```bash
   kubeadm version
   kubectl version --client
   kubelet --version
   ```

   - **Expected Output:** Version information for each component.

---

### **9.3 Disable Swap**

Kubernetes requires swap to be disabled for optimal performance and stability.

#### **Steps to Disable Swap on All Nodes:**

1. **SSH into Each VM:**

   - Use the Bastion Host to SSH into each node.

2. **Disable Swap Immediately:**

   ```bash
   sudo swapoff -a
   ```

3. **Disable Swap Permanently:**

   ```bash
   sudo sed -i '/ swap / s/^/#/' /etc/fstab
   ```

   - **Explanation:** Comments out the swap entry in `/etc/fstab` to prevent swap from enabling on reboot.

4. **Verify Swap is Disabled:**

   ```bash
   free -h
   ```

   - **Expected Output:**

     ```
                   total        used        free      shared  buff/cache   available
     Mem:           7.7G        1.1G        5.9G         69M        0.7G        6.3G
     Swap:            0B          0B          0B
     ```

---

## **Initialize Kubernetes Cluster with `kubeadm`**

With Docker and Kubernetes components installed and configured on all nodes, the next step is to initialize the Kubernetes cluster's Control Plane using `kubeadm`.

### **10.1 Initialize the Control Plane**

#### **Steps to Initialize the Control Plane:**

1. **SSH into the Control Plane Node:**

   ```bash
   ssh -i ~/azure_k8s_rsa azureuser@10.0.2.4
   ```

   - **Replace `10.0.2.4`** with your Control Plane VM's private IP.

2. **Initialize the Kubernetes Cluster:**

   ```bash
   sudo kubeadm init --pod-network-cidr=10.244.0.0/16
   ```

   - **Explanation:**
     - `--pod-network-cidr=10.244.0.0/16`: Specifies the CIDR for the pod network. This value is compatible with Flannel, a popular pod network add-on.

   - **Note:** Keep the `kubeadm join` command outputted at the end; you'll need it to join Worker Nodes.

3. **Configure `kubectl` for the Azureuser:**

   ```bash
   mkdir -p $HOME/.kube
   sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
   sudo chown $(id -u):$(id -g) $HOME/.kube/config
   ```

   - **Explanation:** Sets up `kubectl` to interact with the Kubernetes cluster.

4. **Enable Bash Completion (Optional):**

   ```bash
   echo "source <(kubectl completion bash)" >> ~/.bashrc
   source ~/.bashrc
   ```

5. **Exit the Control Plane Node (if needed):**

   ```bash
   exit
   ```

---

### **10.2 Install a Pod Network Add-on (Flannel)**

A pod network add-on is essential for inter-pod communication within the Kubernetes cluster. **Flannel** is a straightforward and widely-used option.

#### **Steps to Install Flannel:**

1. **SSH into the Control Plane Node:**

   ```bash
   ssh -i ~/azure_k8s_rsa azureuser@10.0.2.4
   ```

2. **Apply the Flannel Network Add-on:**

   ```bash
   kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
   ```

   - **Explanation:** Downloads and applies the Flannel configuration to set up the pod network.

3. **Verify Flannel Pods are Running:**

   ```bash
   kubectl get pods --all-namespaces
   ```

   - **Expected Output:**
     ```
     NAMESPACE     NAME                       READY   STATUS    RESTARTS   AGE
     kube-system   coredns-66bff467f8-abcde   1/1     Running   0          2m
     kube-system   coredns-66bff467f8-fghij   1/1     Running   0          2m
     kube-system   kube-flannel-ds-amd64-12345   1/1     Running   0          2m
     ```

4. **Exit the Control Plane Node (if needed):**

   ```bash
   exit
   ```

---

## **Join Worker Nodes to the Cluster**

With the Control Plane initialized and the pod network configured, the final step is to join the Worker Nodes to the Kubernetes cluster.

### **11.1 Retrieve the `kubeadm join` Command**

After initializing the Control Plane with `kubeadm init`, a `kubeadm join` command is generated. You'll need this command to join each Worker Node to the cluster.

#### **Steps to Retrieve the Join Command:**

1. **SSH into the Control Plane Node:**

   ```bash
   ssh -i ~/azure_k8s_rsa azureuser@10.0.2.4
   ```

2. **Retrieve the Join Command:**

   ```bash
   kubeadm token create --print-join-command
   ```

   - **Output Example:**
     ```bash
     kubeadm join 10.0.2.4:6443 --token abcdef.0123456789abcdef --discovery-token-ca-cert-hash sha256:1234567890abcdef...
     ```

3. **Copy the Join Command:**

   - Note down the entire command as you'll need it for each Worker Node.

4. **Exit the Control Plane Node (if needed):**

   ```bash
   exit
   ```

---

### **11.2 Join Worker Nodes to the Cluster**

For each Worker Node, execute the `kubeadm join` command to integrate them into the Kubernetes cluster.

#### **Steps to Join a Worker Node:**

1. **SSH into the Worker Node:**

   ```bash
   ssh -i ~/azure_k8s_rsa azureuser@10.0.2.5
   ```

   - **Replace `10.0.2.5`** with the private IP of `worker-node-1`. Repeat for `worker-node-2`.

2. **Run the `kubeadm join` Command:**

   ```bash
   sudo kubeadm join 10.0.2.4:6443 --token abcdef.0123456789abcdef --discovery-token-ca-cert-hash sha256:1234567890abcdef...
   ```

   - **Replace `10.0.2.4:6443 --token abcdef.0123456789abcdef --discovery-token-ca-cert-hash sha256:1234567890abcdef...`** with the actual join command retrieved earlier.

3. **Wait for the Join Process to Complete:**

   - The process will download necessary components and integrate the node into the cluster.

   - **Expected Output:**
     ```
     [preflight] Running pre-flight checks
     [preflight] Reading configuration from the cluster...
     [preflight] This might take a minute or two...
     [preflight] Pulling images required for setting up a Kubernetes cluster
     [preflight] This might take a minute or two...
     [join] Running kubeadm join phase preflight
     [join] Running kubeadm join phase kubelet-start
     [join] Running kubeadm join phase kubeconfig
     [join] Running kubeadm join phase control-plane
     [join] Running kubeadm join phase kubelet-finalize
     [join] Joining the cluster
     [join] WARNING: kube-proxy is not running on this node, and network rules will not be updated until kube-proxy is running
     [join] [preflight] If you see this message, your kubelet is successfully configured
     [join] You have successfully joined the cluster
     ```

4. **Exit the Worker Node (if needed):**

   ```bash
   exit
   ```

5. **Repeat for All Worker Nodes:**

   - Perform the above steps for `worker-node-2`.

---

## **Verify the Cluster**

After joining all Worker Nodes, it's essential to verify that the Kubernetes cluster is operational and all nodes are in the `Ready` state.

### **12.1 Check Node Status**

#### **Steps to Check Node Status:**

1. **SSH into the Control Plane Node:**

   ```bash
   ssh -i ~/azure_k8s_rsa azureuser@10.0.2.4
   ```

2. **Retrieve Node Information:**

   ```bash
   kubectl get nodes
   ```

   - **Expected Output:**
     ```
     NAME               STATUS   ROLES           AGE     VERSION
     control-plane-vm   Ready    control-plane   10m     v1.24.3
     worker-node-1      Ready    <none>          8m      v1.24.3
     worker-node-2      Ready    <none>          6m      v1.24.3
     ```

3. **Detailed Node Information (Optional):**

   ```bash
   kubectl describe nodes
   ```

   - **Explanation:** Provides detailed information about each node, including resources, conditions, and allocated workloads.

4. **Exit the Control Plane Node (if needed):**

   ```bash
   exit
   ```

---

### **12.2 Deploy a Test Application**

Deploying a simple application can help verify that the cluster is functioning correctly.

#### **Steps to Deploy a Test Application:**

1. **SSH into the Control Plane Node:**

   ```bash
   ssh -i ~/azure_k8s_rsa azureuser@10.0.2.4
   ```

2. **Create a Deployment:**

   ```bash
   kubectl create deployment nginx --image=nginx
   ```

   - **Explanation:** Deploys an Nginx web server as a Kubernetes Deployment.

3. **Expose the Deployment as a Service:**

   ```bash
   kubectl expose deployment nginx --port=80 --type=NodePort
   ```

   - **Explanation:** Exposes the Nginx Deployment via a Service on port `80` using a NodePort.

4. **Retrieve the Service Details:**

   ```bash
   kubectl get services
   ```

   - **Expected Output:**
     ```
     NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
     kubernetes   ClusterIP   10.0.0.1        <none>        443/TCP        15m
     nginx        NodePort    10.0.2.7        <none>        80:30080/TCP   5m
     ```

   - **Explanation:** The Nginx Service is exposed on port `30080` (NodePort).

5. **Access the Nginx Server:**

   - **From the Bastion Host:**

     ```bash
     curl http://10.0.2.5:30080
     ```

     - **Replace `10.0.2.5`** with the private IP of `worker-node-1` or `worker-node-2`.

   - **Expected Output:**

     ```
     <!DOCTYPE html>
     <html>
     <head>
     <title>Welcome to nginx!</title>
     ...
     </html>
     ```

   - **Explanation:** Displays the default Nginx welcome page, confirming successful deployment.

6. **Cleanup the Test Application (Optional):**

   ```bash
   kubectl delete service nginx
   kubectl delete deployment nginx
   ```

7. **Exit the Control Plane Node (if needed):**

   ```bash
   exit
   ```

---

## **Additional Tips and Best Practices**

1. **Scaling Worker Nodes:**

   - **Manual Scaling:** Repeat the **Create Worker Nodes** steps to add more nodes.
   - **Automated Scaling:** Use **Azure Virtual Machine Scale Sets (VMSS)** for dynamic scaling based on workload demands.

2. **High Availability Control Plane:**

   - **Deploy Multiple Control Plane Nodes:** For production environments, consider deploying multiple Control Plane nodes across different Availability Zones to ensure cluster resilience.

3. **Backup and Restore:**

   - **Regular Backups:** Use tools like **Velero** to back up Kubernetes resources and Persistent Volumes.
   - **Disaster Recovery:** Plan for restoring the cluster in case of failures.

4. **Monitoring and Logging:**

   - **Implement Monitoring Solutions:** Use **Prometheus** and **Grafana** for cluster monitoring.
   - **Azure Monitor:** Integrate with **Azure Monitor** for comprehensive logging and alerting.

5. **Security Enhancements:**

   - **Role-Based Access Control (RBAC):** Implement RBAC to manage user permissions within the cluster.
   - **Network Policies:** Use Kubernetes Network Policies to control traffic between Pods.
   - **Regular Updates:** Keep Kubernetes components and Docker updated to patch security vulnerabilities.

6. **Automation and Infrastructure as Code:**

   - **Use Terraform or Ansible:** Automate cluster deployments and configurations for consistency and efficiency.
   - **Version Control:** Store your infrastructure code in repositories like Git for tracking and collaboration.

7. **Documentation and Backup:**

   - **Maintain Documentation:** Keep detailed records of your configurations, commands, and architecture.
   - **Secure Backups:** Regularly back up SSH keys and critical configurations to prevent loss.

---

## **Troubleshooting**

Encountering issues during the Kubernetes cluster setup is common. Here are some common problems and their solutions:

### **1. Nodes Not Ready**

- **Cause:** Pod network add-on not properly installed or network communication issues.
- **Solution:**
  - Verify that Flannel (or your chosen pod network) is correctly deployed.
  - Check NSG rules to ensure necessary ports are open.
  - Use `kubectl get pods --all-namespaces` to inspect pod statuses.

### **2. SSH Access Issues**

- **Cause:** Incorrect NSG rules, SSH keys not properly configured, or firewall settings on VMs.
- **Solution:**
  - Ensure Bastion NSG allows SSH from your IP.
  - Verify that Kubernetes NSG allows SSH from Bastion Subnet.
  - Check firewall settings (`ufw status`) on VMs.

### **3. `kubeadm init` Errors**

- **Cause:** Resource constraints, swap not disabled, or conflicting network configurations.
- **Solution:**
  - Ensure VMs meet the minimum resource requirements.
  - Confirm swap is disabled (`free -h` should show swap as `0`).
  - Review `kubeadm` logs for detailed error messages.

### **4. Application Deployment Failures**

- **Cause:** Insufficient resources, incorrect configurations, or network issues.
- **Solution:**
  - Check pod logs using `kubectl logs <pod-name>`.
  - Inspect events with `kubectl get events`.
  - Verify resource allocations on nodes.

### **5. Unable to Access Services via NodePort**

- **Cause:** NSG rules not allowing traffic on NodePort range or service misconfiguration.
- **Solution:**
  - Ensure NSG allows traffic on ports `30000-32767`.
  - Verify service type is `NodePort` and correctly exposed.
  - Use `curl` from Bastion Host to test connectivity.

---

## **References**

- [Azure Portal Documentation](https://docs.microsoft.com/azure/azure-portal/)
- [Azure CLI Documentation](https://docs.microsoft.com/cli/azure/)
- [Kubernetes Documentation](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)
- [Flannel Networking](https://github.com/coreos/flannel)
- [Azure Virtual Network Documentation](https://docs.microsoft.com/azure/virtual-network/)
- [Azure Bastion Documentation](https://docs.microsoft.com/azure/bastion/bastion-overview)
- [Velero Backup for Kubernetes](https://velero.io/docs/)
- [Prometheus Monitoring](https://prometheus.io/docs/introduction/overview/)
- [Grafana Monitoring](https://grafana.com/docs/grafana/latest/)
- [Terraform for Azure](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs)
- [Ansible for Kubernetes](https://docs.ansible.com/ansible/latest/collections/community/kubernetes/k8s_module.html)


