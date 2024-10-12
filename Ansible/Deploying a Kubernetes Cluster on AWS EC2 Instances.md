# Ansible Guide: Deploying a Kubernetes Cluster on AWS EC2 Instances Using Kubeadm

## Table of Contents

1. [Introduction](#introduction)
2. [Prerequisites](#prerequisites)
3. [Setting Up the Environment](#setting-up-the-environment)
   - [AWS EC2 Overview](#aws-ec2-overview)
   - [SSH Access Configuration](#ssh-access-configuration)
4. [Installing Ansible on the Control Machine](#installing-ansible-on-the-control-machine)
5. [Creating the Ansible Inventory](#creating-the-ansible-inventory)
6. [Writing the Ansible Playbook](#writing-the-ansible-playbook)
   - [Playbook Structure](#playbook-structure)
   - [Variables and Templates](#variables-and-templates)
7. [Deploying Kubernetes Components](#deploying-kubernetes-components)
   - [Installing Dependencies](#installing-dependencies)
   - [Installing Container Runtime](#installing-container-runtime)
   - [Installing Kubernetes Components](#installing-kubernetes-components)
   - [Disabling Swap Memory](#disabling-swap-memory)
   - [Initializing the Control Plane Node](#initializing-the-control-plane-node)
   - [Joining Worker Nodes to the Cluster](#joining-worker-nodes-to-the-cluster)
8. [Verifying the Kubernetes Cluster](#verifying-the-kubernetes-cluster)
9. [Best Practices and Troubleshooting](#best-practices-and-troubleshooting)
10. [Conclusion](#conclusion)

---

## Introduction

This guide provides a comprehensive walkthrough on using **Ansible** to automate the deployment of a **Kubernetes** cluster with **kubeadm** on three AWS EC2 instances. You will learn how to:

- Install and configure Kubernetes components on multiple nodes.
- Automate the initialization of the control plane node.
- Join worker nodes to the Kubernetes cluster.
- Verify the successful deployment of the cluster.

---

## Prerequisites

Before proceeding, ensure you have the following:

- **Ansible Control Machine**: A Linux machine with Ansible installed (this can be your local machine or an AWS EC2 instance).
- **AWS EC2 Instances**:
  - **controlplane**: The Kubernetes control plane node.
  - **node1** and **node2**: Kubernetes worker nodes.
- **SSH Access**: Passwordless SSH access from the Ansible control machine to all EC2 instances.
- **Internet Access**: All instances must have internet access to download packages and container images.
- **AWS CLI** (optional): For managing AWS resources.

---

## Setting Up the Environment

### AWS EC2 Overview

Your AWS setup includes:

- **controlplane EC2 Instance**: Acts as the Kubernetes master node.
- **node1 EC2 Instance**: Kubernetes worker node.
- **node2 EC2 Instance**: Kubernetes worker node.

Ensure all instances are running a supported OS (e.g., Ubuntu 20.04 LTS).

#### Security Groups

- **Ports to Open**:
  - **SSH (22)**: For SSH access.
  - **Kubernetes API Server (6443)**: Required for communication with the Kubernetes API.
  - **Node Ports (30000-32767)**: For accessing NodePort services.
  - **Custom Ports**: Any additional ports required by your applications.
- **Inbound Rules**: Configure security groups to allow necessary inbound traffic.
- **Outbound Rules**: Allow outbound traffic to the internet for package downloads.

### SSH Access Configuration

1. **Generate SSH Keys** (if not already done):

   ```bash
   ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
   ```

2. **Copy Public Key to EC2 Instances**:

   - **Option 1: During Instance Creation**

     - When launching EC2 instances, associate your SSH key pair.

   - **Option 2: Manually Copying Keys**

     - If required, you can copy your public key to the instances using the default `ec2-user` or `ubuntu` user, depending on the AMI.

     ```bash
     ssh-copy-id ubuntu@controlplane_public_ip
     ssh-copy-id ubuntu@node1_public_ip
     ssh-copy-id ubuntu@node2_public_ip
     ```

     *Replace `ubuntu` with the appropriate username if you're using a different AMI.*

3. **Test SSH Connections**:

   ```bash
   ssh ubuntu@controlplane_public_ip
   ssh ubuntu@node1_public_ip
   ssh ubuntu@node2_public_ip
   ```

   *Ensure you can SSH into all instances without being prompted for a password.*

---

## Installing Ansible on the Control Machine

Install Ansible on your control machine if it's not already installed.

### On Ubuntu/Debian Control Machine

```bash
sudo apt update
sudo apt install ansible -y
```

### Verify Ansible Installation

```bash
ansible --version
```

---

## Creating the Ansible Inventory

Create an inventory file named `hosts.ini` in your Ansible project directory.

```ini
[controlplane]
controlplane ansible_host=<CONTROLPLANE_PUBLIC_IP> ansible_user=ubuntu

[workers]
node1 ansible_host=<NODE1_PUBLIC_IP> ansible_user=ubuntu
node2 ansible_host=<NODE2_PUBLIC_IP> ansible_user=ubuntu

[kubernetes:children]
controlplane
workers
```

*Replace `<CONTROLPLANE_PUBLIC_IP>`, `<NODE1_PUBLIC_IP>`, and `<NODE2_PUBLIC_IP>` with your actual EC2 instance public IP addresses. Adjust `ansible_user` if your instances use a different default username.*

---

## Writing the Ansible Playbook

### Playbook Structure

Create a playbook file named `kubernetes_cluster.yml`.

```yaml
- hosts: all
  become: yes
  vars:
    kube_version: "1.26.0-00"
  tasks:
    - name: Install dependencies
      apt:
        name:
          - apt-transport-https
          - ca-certificates
          - curl
          - gnupg
          - lsb-release
        state: present
        update_cache: yes

    # Additional tasks will be added here
```

### Variables and Templates

- **Variables**: Define variables like `kube_version` for consistent versioning across all nodes.
- **Templates**: Use Jinja2 templates for configuration files if customization is needed.

---

## Deploying Kubernetes Components

### Installing Dependencies

Dependencies are already included in the initial playbook structure.

### Installing Container Runtime

Kubernetes requires a container runtime. Docker is deprecated as a runtime in newer Kubernetes versions. We'll use **containerd**.

```yaml
    - name: Install containerd prerequisites
      apt:
        name:
          - gnupg2
          - software-properties-common
        state: present

    - name: Add Docker's official GPG key
      apt_key:
        url: https://download.docker.com/linux/ubuntu/gpg
        state: present

    - name: Set up Docker repository
      apt_repository:
        repo: deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable
        state: present

    - name: Install containerd
      apt:
        name: containerd.io
        state: present
        update_cache: yes

    - name: Configure containerd and restart
      shell: |
        sudo mkdir -p /etc/containerd
        containerd config default | sudo tee /etc/containerd/config.toml
        sudo sed -i 's/SystemdCgroup = false/SystemdCgroup = true/' /etc/containerd/config.toml
      args:
        warn: false

    - name: Restart containerd
      systemd:
        name: containerd
        state: restarted
        enabled: yes
```

### Installing Kubernetes Components

Add tasks to install `kubeadm`, `kubelet`, and `kubectl`.

```yaml
    - name: Add Kubernetes GPG key
      apt_key:
        url: https://packages.cloud.google.com/apt/doc/apt-key.gpg
        state: present

    - name: Add Kubernetes APT repository
      apt_repository:
        repo: deb http://apt.kubernetes.io/ kubernetes-xenial main
        state: present

    - name: Install Kubernetes components
      apt:
        name:
          - kubeadm={{ kube_version }}
          - kubelet={{ kube_version }}
          - kubectl={{ kube_version }}
        state: present
        update_cache: yes

    - name: Hold Kubernetes packages at current version
      apt:
        name:
          - kubeadm
          - kubelet
          - kubectl
        state: present
        mark_hold: yes
```

### Disabling Swap Memory

Kubernetes requires swap to be disabled on all nodes.

```yaml
    - name: Disable swap
      shell: swapoff -a
      args:
        warn: false

    - name: Remove swap entry from /etc/fstab
      replace:
        path: /etc/fstab
        regexp: '^(.*/swap.*)$'
        replace: '# \1'
```

### Adjusting sysctl Settings

Configure sysctl settings required by Kubernetes.

```yaml
    - name: Enable net.bridge.bridge-nf-call-iptables
      sysctl:
        name: net.bridge.bridge-nf-call-iptables
        value: '1'
        state: present
        reload: yes

    - name: Enable net.ipv4.ip_forward
      sysctl:
        name: net.ipv4.ip_forward
        value: '1'
        state: present
        reload: yes
```

### Initializing the Control Plane Node

Add a play specifically for the control plane node.

```yaml
- hosts: controlplane
  become: yes
  vars:
    pod_network_cidr: "192.168.0.0/16"
  tasks:
    - name: Initialize Kubernetes cluster
      command: kubeadm init --pod-network-cidr={{ pod_network_cidr }} --ignore-preflight-errors=all
      args:
        creates: /etc/kubernetes/admin.conf

    - name: Create .kube directory
      file:
        path: /home/{{ ansible_user }}/.kube
        state: directory
        owner: "{{ ansible_user }}"
        group: "{{ ansible_user }}"

    - name: Copy admin.conf to user's kube config
      copy:
        src: /etc/kubernetes/admin.conf
        dest: /home/{{ ansible_user }}/.kube/config
        owner: "{{ ansible_user }}"
        group: "{{ ansible_user }}"
        mode: '0644'

    - name: Install Calico network plugin
      become: false
      command: kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
```

### Retrieving Join Command

Add a task to retrieve the `kubeadm join` command on the control plane node.

```yaml
    - name: Get join command
      command: kubeadm token create --print-join-command
      register: join_command_output

    - name: Set join command fact
      set_fact:
        join_command: "{{ join_command_output.stdout }}"
```

### Joining Worker Nodes to the Cluster

Create a play for the worker nodes to join the cluster.

```yaml
- hosts: workers
  become: yes
  vars:
    join_command: "{{ hostvars['controlplane']['join_command'] }}"
  tasks:
    - name: Join the cluster
      command: "{{ join_command }} --ignore-preflight-errors=all"
      args:
        creates: /etc/kubernetes/kubelet.conf
```

---

## Verifying the Kubernetes Cluster

After running the playbook, verify the cluster is up and running.

1. **Connect to the Control Plane Instance**:

   ```bash
   ssh ubuntu@controlplane_public_ip
   ```

2. **Check Nodes Status**:

   ```bash
   kubectl get nodes
   ```

   You should see all three nodes (`controlplane`, `node1`, `node2`) in the `Ready` status.

3. **Check Pods in All Namespaces**:

   ```bash
   kubectl get pods --all-namespaces
   ```

   Ensure that all system pods are running correctly.

---

## Best Practices and Troubleshooting

### Best Practices

- **Security Groups**: Limit access to necessary ports and from trusted IP ranges.
- **Use Private IPs**: If possible, use private IPs for cluster communication to reduce exposure.
- **Regular Updates**: Keep Kubernetes components up to date with security patches.
- **IAM Roles**: Use appropriate IAM roles and policies if integrating with AWS services.
- **Ansible Vault**: Use Ansible Vault to encrypt sensitive data like passwords or tokens.

### Troubleshooting Tips

- **Ansible Verbose Mode**: Use `-vvv` for detailed output during playbook execution.
- **SSH Connectivity**: Ensure the Ansible control machine can SSH into all nodes without issues.
- **Swap Disabled**: Verify that swap is disabled on all nodes.
- **Firewall Rules**: Ensure that the necessary ports are open between the nodes.
- **Check Logs**: On nodes, check logs for kubelet and kubeadm if issues arise.
- **Re-initialize Nodes**: If a node fails to join, you may need to reset it using `kubeadm reset` and try again.

---

## Conclusion

By following this guide, you have automated the deployment of a Kubernetes cluster on AWS EC2 instances using Ansible and kubeadm. This setup can be scaled and customized to fit your specific requirements.

### Next Steps

- **Deploy Applications**: Begin deploying containerized applications to your Kubernetes cluster.
- **Implement Monitoring**: Set up monitoring tools like Prometheus and Grafana for cluster health.
- **Configure Storage**: Integrate persistent storage solutions as needed.
- **Explore AWS Integrations**: Utilize AWS services like EFS, ELB, and IAM roles with your cluster.
