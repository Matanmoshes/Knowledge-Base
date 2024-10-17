# Building a Kubernetes 1.27 Cluster with kubeadm

## Introduction

This guide will walk you through the process of building a Kubernetes 1.27 cluster using `kubeadm`. By the end of this lab, you will have a functioning Kubernetes cluster deployed across a set of Linux servers. This hands-on experience will equip you with the essential skills to create and manage Kubernetes clusters in real-world environments.

---
## Prerequisites

- **Linux Servers**: You will need three Linux servers (one control plane node and two worker nodes) with the following specifications:
  - **Operating System**: Ubuntu 20.04 or later
  - **CPU**: Minimum 2 CPUs
  - **Memory**: Minimum 2 GB RAM per machine
  - **Network**: Full network connectivity between all machines

- **User Access**: SSH access to all servers with `sudo` privileges.

- **Basic Knowledge**: Familiarity with Linux command-line operations and basic Kubernetes concepts.

---
## Guide

### Step 1: Log In to the Lab Servers

Use the provided credentials to log in to each of the Linux servers. Replace `<PUBLIC_IP_ADDRESS>` with the actual IP addresses of your servers.

```bash
ssh cloud_user@<PUBLIC_IP_ADDRESS>
```

---
### Step 2: Install Required Packages on All Nodes

Perform the following steps on **all three nodes** (one control plane and two workers).

#### a. Load Kernel Modules

Create a configuration file to load necessary kernel modules.

```bash
sudo tee /etc/modules-load.d/containerd.conf <<EOF
overlay
br_netfilter
EOF
```

Load the modules immediately without rebooting.

```bash
sudo modprobe overlay
sudo modprobe br_netfilter
```

#### b. Set System Configurations for Kubernetes Networking

Configure the system to allow Kubernetes networking to function correctly.

```bash
sudo tee /etc/sysctl.d/99-kubernetes-cri.conf <<EOF
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
net.bridge.bridge-nf-call-ip6tables = 1
EOF
```

Apply the new settings.

```bash
sudo sysctl --system
```

#### c. Install `containerd`

`containerd` is the container runtime required by Kubernetes.

1. **Update Package List and Install `containerd`**

   ```bash
   sudo apt-get update
   sudo apt-get install -y containerd.io
   ```

2. **Configure `containerd`**

   Create the necessary directory and generate the default configuration.

   ```bash
   sudo mkdir -p /etc/containerd
   sudo containerd config default | sudo tee /etc/containerd/config.toml
   ```

3. **Restart and Enable `containerd`**

   Restart the `containerd` service to apply the configuration and ensure it starts on boot.

   ```bash
   sudo systemctl restart containerd
   sudo systemctl enable containerd
   ```

4. **Verify `containerd` is Running**

   ```bash
   sudo systemctl status containerd
   ```

   Ensure that the service is active and running.

#### d. Disable Swap

Kubernetes requires swap to be disabled to function correctly.

```bash
sudo swapoff -a
```

To make this change permanent, comment out any swap entries in `/etc/fstab`.

```bash
sudo sed -i '/ swap / s/^/#/' /etc/fstab
```

#### e. Install Kubernetes Packages

1. **Install Dependencies**

   ```bash
   sudo apt-get update
   sudo apt-get install -y apt-transport-https curl
   ```

2. **Add Kubernetes GPG Key**

   ```bash
   curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.27/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
   ```

3. **Add Kubernetes Repository**

   ```bash
   sudo tee /etc/apt/sources.list.d/kubernetes.list <<EOF
deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.27/deb/ /
EOF
   ```

4. **Update Package Listings**

   ```bash
   sudo apt-get update
   ```

5. **Install `kubelet`, `kubeadm`, and `kubectl`**

   ```bash
   sudo apt-get install -y kubelet kubeadm kubectl
   ```

   > **Note**: If you encounter a `dpkg lock` message, wait for a minute or two before retrying the command.

6. **Prevent Kubernetes Packages from Being Updated Automatically**

   ```bash
   sudo apt-mark hold kubelet kubeadm kubectl
   ```

---
### Step 3: Initialize the Kubernetes Cluster on the Control Plane Node

Only perform the following steps on the **control plane node**.

#### a. Initialize the Cluster

Use `kubeadm` to initialize the cluster. The `--pod-network-cidr` flag specifies the CIDR for the pod network (Calico requires `192.168.0.0/16`).

```bash
sudo kubeadm init --pod-network-cidr=192.168.0.0/16 --kubernetes-version=1.27.11
```

#### b. Configure `kubectl` for the Control Plane User

Set up the local `kubectl` configuration to manage the cluster.

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

#### c. Verify Cluster Access

Check the status of the nodes to ensure the control plane is up and running.

```bash
kubectl get nodes
```

You should see the control plane node listed as `Ready`.

---

### Step 4: Install the Calico Network Add-On

Calico provides networking and network policy for Kubernetes.

```bash
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.25.0/manifests/calico.yaml
```

#### Verify Calico Installation

After applying the Calico manifest, verify that all pods are running correctly.

```bash
kubectl get pods -n kube-system
```

All Calico pods should be in the `Running` state.

---
### Step 5: Join Worker Nodes to the Cluster

Perform the following steps on **each worker node**.

#### a. Retrieve the Join Command

On the **control plane node**, generate the `kubeadm join` command. This command includes a token and the hash required for secure communication.

```bash
kubeadm token create --print-join-command
```

The output will resemble:

```bash
kubeadm join <CONTROL_PLANE_IP>:6443 --token <TOKEN> --discovery-token-ca-cert-hash sha256:<HASH>
```

#### b. Execute the Join Command on Worker Nodes

Copy the entire `kubeadm join` command from the control plane node and execute it on each worker node.

```bash
sudo kubeadm join <CONTROL_PLANE_IP>:6443 --token <TOKEN> --discovery-token-ca-cert-hash sha256:<HASH>
```

> **Note**: Ensure you run this command as `sudo` to have the necessary privileges.

#### c. Verify Worker Nodes are Joined

After a few minutes, return to the **control plane node** and check the status of all nodes.

```bash
kubectl get nodes
```

You should see all worker nodes listed as `Ready`. It may take a short while for the nodes to transition to the `Ready` state.

---

### Step 6: Final Verification

Ensure that all components of your Kubernetes cluster are functioning correctly.

1. **Check Node Status**

   ```bash
   kubectl get nodes
   ```

   Example output:

   ```
   NAME             STATUS   ROLES    AGE   VERSION
   control-plane    Ready    Master   10m   v1.27.11
   worker-node-1    Ready    <none>   8m    v1.27.11
   worker-node-2    Ready    <none>   8m    v1.27.11
   ```

2. **Check Pod Status in All Namespaces**

   ```bash
   kubectl get pods --all-namespaces
   ```

   Ensure that all system pods are running without issues.

3. **Deploy a Test Application (Optional)**

   Deploy a simple application to test the cluster functionality.

   ```bash
   kubectl create deployment nginx --image=nginx
   kubectl expose deployment nginx --port=80 --type=NodePort
   kubectl get services
   ```

   Access the Nginx service via the worker node's IP and the assigned NodePort.


---

## Conclusion

### Next Steps

- **Explore Kubernetes Features**: Deploy various applications, set up namespaces, and experiment with Kubernetes features like Deployments, Services, and ConfigMaps.
  
- **Implement Security Best Practices**: Configure RBAC, network policies, and other security measures to secure your cluster.

- **Scale the Cluster**: Add more worker nodes or set up high-availability control planes for a more resilient cluster setup.

- **Monitor and Maintain**: Implement monitoring solutions like Prometheus and Grafana to keep track of your cluster's health and performance.

Feel free to refer to the [official Kubernetes documentation](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/) for more detailed information and advanced configurations.