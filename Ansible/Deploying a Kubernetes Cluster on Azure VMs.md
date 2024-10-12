# Ansible Guide: Deploying a Kubernetes Cluster on Azure VMs Using Kubeadm

## Table of Contents

1. [Introduction](#introduction)
2. [Prerequisites](#prerequisites)
3. [Setting Up the Environment](#setting-up-the-environment)
   - [Azure VMs Overview](#azure-vms-overview)
   - [SSH Access Configuration](#ssh-access-configuration)
4. [Installing Ansible on the Control Machine](#installing-ansible-on-the-control-machine)
5. [Creating the Ansible Inventory](#creating-the-ansible-inventory)
6. [Writing the Ansible Playbook](#writing-the-ansible-playbook)
   - [Playbook Structure](#playbook-structure)
   - [Variables and Templates](#variables-and-templates)
7. [Deploying Kubernetes Components](#deploying-kubernetes-components)
   - [Installing Dependencies](#installing-dependencies)
   - [Installing Docker](#installing-docker)
   - [Installing Kubernetes Components](#installing-kubernetes-components)
   - [Initializing the Control Plane Node](#initializing-the-control-plane-node)
   - [Joining Worker Nodes to the Cluster](#joining-worker-nodes-to-the-cluster)
8. [Verifying the Kubernetes Cluster](#verifying-the-kubernetes-cluster)
9. [Best Practices and Troubleshooting](#best-practices-and-troubleshooting)
10. [Conclusion](#conclusion)

---

## Introduction

This guide provides a step-by-step walkthrough on how to use **Ansible** to automate the deployment of a **Kubernetes** cluster using **kubeadm** on three Azure Virtual Machines (VMs). You will learn how to:

- Install and configure Kubernetes components on multiple nodes.
- Automate the initialization of the control plane node.
- Join worker nodes to the Kubernetes cluster.
- Verify the successful deployment of the cluster.

---

## Prerequisites

Before proceeding, ensure you have the following:

- **Ansible Control Machine**: A Linux machine with Ansible installed (can be your local machine or a dedicated VM).
- **Azure VMs**:
  - **controlplane**: The control plane node.
  - **node1** and **node2**: Worker nodes.
- **SSH Access**: Passwordless SSH access from the Ansible control machine to all Azure VMs.
- **Internet Access**: All VMs must have internet access to download packages and Docker images.
- **Azure CLI** (optional): For managing Azure resources.

---

## Setting Up the Environment

### Azure VMs Overview

Your Azure setup includes:

- **controlplane VM**: Acts as the Kubernetes master node.
- **node1 VM**: Kubernetes worker node.
- **node2 VM**: Kubernetes worker node.

Ensure all VMs are running a supported OS (e.g., Ubuntu 20.04 LTS).

### SSH Access Configuration

1. **Generate SSH Keys** (if not already done):

   ```bash
   ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
   ```

2. **Copy Public Key to VMs**:

   ```bash
   ssh-copy-id user@controlplane
   ssh-copy-id user@node1
   ssh-copy-id user@node2
   ```

   *Replace `user` with the username on the Azure VMs.*

3. **Test SSH Connections**:

   ```bash
   ssh user@controlplane
   ssh user@node1
   ssh user@node2
   ```

---

## Installing Ansible on the Control Machine

Install Ansible on your control machine if not already installed.

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
controlplane ansible_host=<CONTROLPLANE_IP> ansible_user=<USERNAME>

[workers]
node1 ansible_host=<NODE1_IP> ansible_user=<USERNAME>
node2 ansible_host=<NODE2_IP> ansible_user=<USERNAME>

[kubernetes:children]
controlplane
workers
```

*Replace `<CONTROLPLANE_IP>`, `<NODE1_IP>`, `<NODE2_IP>`, and `<USERNAME>` with your actual VM IP addresses and username.*

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

- **Variables**: Define variables like `kube_version` for consistent versioning.
- **Templates**: Use Jinja2 templates for configuration files if needed.

---

## Deploying Kubernetes Components

### Installing Dependencies

Already included in the initial playbook structure.

### Installing Docker

Add the following tasks to install Docker.

```yaml
    - name: Add Docker GPG key
      apt_key:
        url: https://download.docker.com/linux/ubuntu/gpg
        state: present

    - name: Add Docker APT repository
      apt_repository:
        repo: deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable
        state: present

    - name: Install Docker
      apt:
        name: docker-ce
        state: present
        update_cache: yes

    - name: Add user to Docker group
      user:
        name: "{{ ansible_user }}"
        groups: docker
        append: yes
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

Kubernetes requires swap to be disabled.

```yaml
    - name: Disable swap
      command: swapoff -a
      when: ansible_swaptotal_mb > 0

    - name: Remove swap entry from /etc/fstab
      replace:
        path: /etc/fstab
        regexp: '^([^#]*\s)swap(\s.*)$'
        replace: '#\1swap\2'
```

### Initializing the Control Plane Node

Add a play specifically for the control plane.

```yaml
- hosts: controlplane
  become: yes
  vars:
    pod_network_cidr: "192.168.0.0/16"
  tasks:
    - name: Initialize Kubernetes cluster
      command: kubeadm init --apiserver-advertise-address={{ ansible_default_ipv4.address }} --pod-network-cidr={{ pod_network_cidr }}
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

Add a task to retrieve the `kubeadm join` command.

```yaml
    - name: Get join command
      command: kubeadm token create --print-join-command
      register: join_command_output

    - name: Set join command fact
      set_fact:
        join_command: "{{ join_command_output.stdout }}"
```

### Joining Worker Nodes to the Cluster

Create a play for the worker nodes.

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

After running the playbook, verify the cluster.

1. **Connect to the Control Plane VM**:

   ```bash
   ssh user@controlplane
   ```

2. **Check Nodes Status**:

   ```bash
   kubectl get nodes
   ```

   You should see all three nodes (`controlplane`, `node1`, `node2`) in the `Ready` status.

---

## Best Practices and Troubleshooting

### Best Practices

- **Version Consistency**: Ensure all nodes run the same Kubernetes version.
- **Secure SSH Access**: Use key-based authentication and manage SSH keys securely.
- **Inventory Management**: Use descriptive hostnames and groupings.
- **Ansible Vault**: Secure sensitive data with Ansible Vault if needed.

### Troubleshooting Tips

- **Check Ansible Logs**: Use `-vvv` with Ansible commands for verbose output.
- **SSH Connectivity**: Ensure Ansible control machine can SSH into all nodes.
- **Swap Disabled**: Kubernetes requires swap to be disabled on all nodes.
- **Firewall Rules**: Open necessary ports or disable firewalls during testing.
- **SELinux**: Set SELinux to permissive mode on RedHat-based systems if issues arise.

---

## Conclusion

By following this guide, you've automated the deployment of a Kubernetes cluster on Azure VMs using Ansible and kubeadm. This setup is scalable and can be extended to include more worker nodes or additional configurations.

### Next Steps

- **Deploy Applications**: Start deploying applications to your new Kubernetes cluster.
- **Cluster Management**: Explore Kubernetes features like deployments, services, and config maps.
- **Monitoring and Logging**: Implement monitoring solutions like Prometheus and Grafana.
