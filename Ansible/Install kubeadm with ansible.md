## Playbook:

```yaml
---
- name: Kubernetes Cluster Setup
  hosts: all
  become: yes
  vars:
    kubernetes_version: '1.29.0'
    pod_network_cidr: '192.168.0.0/16'
  tasks:
    - name: Update and upgrade apt packages
      apt:
        update_cache: yes
        upgrade: dist

    - name: Disable swap
      command: swapoff -a
      when: ansible_swaptotal_mb > 0

    - name: Remove swap entry from /etc/fstab
      replace:
        path: /etc/fstab
        regexp: '^([^#]*\s+swap\s+.*)$'
        replace: '# \1'
        backup: yes

    - name: Load necessary kernel modules configuration
      copy:
        dest: /etc/modules-load.d/containerd.conf
        content: |
          overlay
          br_netfilter

    - name: Ensure kernel modules are loaded
      command: modprobe {{ item }}
      loop:
        - overlay
        - br_netfilter
      ignore_errors: yes

    - name: Set system configurations for Kubernetes
      copy:
        dest: /etc/sysctl.d/kubernetes.conf
        content: |
          net.bridge.bridge-nf-call-ip6tables  = 1
          net.bridge.bridge-nf-call-iptables   = 1
          net.ipv4.ip_forward                  = 1

    - name: Apply sysctl params
      command: sysctl --system

    - name: Install containerd prerequisites
      apt:
        name:
          - curl
          - gnupg2
          - software-properties-common
          - apt-transport-https
          - ca-certificates
          - socat
        state: present

    - name: Install containerd and runc
      apt:
        name:
          - containerd
          - runc
        state: present

    - name: Create containerd configuration directory
      file:
        path: /etc/containerd
        state: directory

    - name: Generate default containerd configuration
      command: containerd config default > /etc/containerd/config.toml
      args:
        creates: /etc/containerd/config.toml

    - name: Set SystemdCgroup to true in containerd config
      replace:
        path: /etc/containerd/config.toml
        regexp: '^(\s*)SystemdCgroup = false'
        replace: '\1SystemdCgroup = true'

    - name: Restart and enable containerd service
      systemd:
        name: containerd.service
        state: restarted
        enabled: yes

    - name: Add Kubernetes GPG key
      apt_key:
        url: https://pkgs.k8s.io/core:/stable:/v1.29/deb/Release.key
        state: present
        keyring: /etc/apt/keyrings/kubernetes-apt-keyring.gpg

    - name: Add Kubernetes apt repository
      apt_repository:
        repo: "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.29/deb/ /"
        state: present
        filename: kubernetes

    - name: Update apt cache after adding Kubernetes repo
      apt:
        update_cache: yes

    - name: Install Kubernetes components
      apt:
        name:
          - kubelet
          - kubeadm
          - kubectl
        state: present
        allow_downgrade: yes

    - name: Hold Kubernetes packages at current version
      command: apt-mark hold kubelet kubeadm kubectl

- name: Initialize Kubernetes Control Plane Node
  hosts: master
  become: yes
  vars:
    pod_network_cidr: '192.168.0.0/16'
  tasks:
    - name: Initialize Kubernetes cluster
      command: kubeadm init --pod-network-cidr={{ pod_network_cidr }}
      args:
        creates: /etc/kubernetes/admin.conf
      register: kubeadm_init

    - name: Create .kube directory
      file:
        path: /home/{{ ansible_user }}/.kube
        state: directory
        owner: "{{ ansible_user }}"
        mode: 0755

    - name: Copy admin.conf to user's kube config
      copy:
        src: /etc/kubernetes/admin.conf
        dest: /home/{{ ansible_user }}/.kube/config
        owner: "{{ ansible_user }}"
        mode: 0644
        remote_src: yes

    - name: Generate kubeadm join command
      command: kubeadm token create --print-join-command
      register: kubeadm_join_command

    - name: Set kubeadm join command as fact
      set_fact:
        kubeadm_join_cmd: "{{ kubeadm_join_command.stdout }}"

    - name: Install Calico network plugin
      shell: kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.25.0/manifests/calico.yaml
      environment:
        KUBECONFIG: /etc/kubernetes/admin.conf

- name: Join Worker Nodes to Cluster
  hosts: workers
  become: yes
  tasks:
    - name: Retrieve kubeadm join command from master
      set_fact:
        kubeadm_join_cmd: "{{ hostvars[groups['master'][0]]['kubeadm_join_cmd'] }}"

    - name: Join the node to the Kubernetes cluster
      command: "{{ kubeadm_join_cmd }}"
      args:
        creates: /etc/kubernetes/kubelet.conf

```


---

# Ansible Playbook Breakdown Guide: Setting Up a Kubernetes Cluster

This guide provides a step-by-step breakdown of an Ansible playbook designed to automate the setup of a Kubernetes cluster with one control plane node (master) and two worker nodes. The playbook performs system preparation, installs necessary components, initializes the master node, and joins worker nodes to the cluster.

## Table of Contents

1. [Introduction](#introduction)
2. [Prerequisites](#prerequisites)
3. [Playbook Structure](#playbook-structure)
4. [Detailed Breakdown](#detailed-breakdown)
   - [Play 1: Kubernetes Cluster Setup](#play-1-kubernetes-cluster-setup)
   - [Play 2: Initialize Kubernetes Control Plane Node](#play-2-initialize-kubernetes-control-plane-node)
   - [Play 3: Join Worker Nodes to Cluster](#play-3-join-worker-nodes-to-cluster)
5. [Conclusion](#conclusion)

---

## Introduction

This Ansible playbook automates the deployment of a Kubernetes cluster by:

- Preparing all nodes (master and workers) with the necessary system configurations and installations.
- Initializing the Kubernetes control plane on the master node.
- Joining the worker nodes to the cluster.

By automating these steps, the playbook ensures consistency across all nodes and reduces the potential for human error.

## Prerequisites

- **Ansible Installed**: Ensure that Ansible is installed on the control machine from which the playbook will be executed.
- **Inventory Setup**: An inventory file defining `master` and `workers` groups.
- **SSH Access**: Passwordless SSH access from the control machine to all nodes.
- **Supported OS**: The nodes should be running a Debian-based Linux distribution (e.g., Ubuntu).

## Playbook Structure

The playbook consists of three main plays:

1. **Kubernetes Cluster Setup**: Runs on all nodes to perform system preparation and install necessary components.
2. **Initialize Kubernetes Control Plane Node**: Runs on the master node to initialize the Kubernetes cluster.
3. **Join Worker Nodes to Cluster**: Runs on worker nodes to join them to the Kubernetes cluster.

---

## Detailed Breakdown

### Play 1: Kubernetes Cluster Setup

**Purpose**: Prepare all nodes by updating packages, configuring system settings, and installing container runtime and Kubernetes components.

**Hosts**: `all`

**Variables**:

- `kubernetes_version`: Version of Kubernetes to install (e.g., `1.29.0`).
- `pod_network_cidr`: CIDR for the pod network (e.g., `192.168.0.0/16`).

#### Tasks:

1. **Update and Upgrade Apt Packages**

   ```yaml
   - name: Update and upgrade apt packages
     apt:
       update_cache: yes
       upgrade: dist
   ```

   - **Explanation**: Updates the package lists and upgrades installed packages to the latest versions.

2. **Disable Swap**

   ```yaml
   - name: Disable swap
     command: swapoff -a
     when: ansible_swaptotal_mb > 0
   ```

   - **Explanation**: Disables swap memory, which is required for Kubernetes to function properly.
   - **Condition**: Runs only if the system has swap enabled (`ansible_swaptotal_mb > 0`).

3. **Remove Swap Entry from `/etc/fstab`**

   ```yaml
   - name: Remove swap entry from /etc/fstab
     replace:
       path: /etc/fstab
       regexp: '^([^#]*\s+swap\s+.*)$'
       replace: '# \1'
       backup: yes
   ```

   - **Explanation**: Comments out the swap entry in `/etc/fstab` to prevent swap from being enabled on reboot.
   - **Backup**: Creates a backup of the original `/etc/fstab`.

4. **Load Necessary Kernel Modules Configuration**

   ```yaml
   - name: Load necessary kernel modules configuration
     copy:
       dest: /etc/modules-load.d/containerd.conf
       content: |
         overlay
         br_netfilter
   ```

   - **Explanation**: Creates a configuration file to ensure that the `overlay` and `br_netfilter` kernel modules are loaded on boot.

5. **Ensure Kernel Modules are Loaded**

   ```yaml
   - name: Ensure kernel modules are loaded
     command: modprobe {{ item }}
     loop:
       - overlay
       - br_netfilter
     ignore_errors: yes
   ```

   - **Explanation**: Loads the `overlay` and `br_netfilter` kernel modules immediately.
   - **Error Handling**: Ignores errors in case the modules are already loaded.

6. **Set System Configurations for Kubernetes**

   ```yaml
   - name: Set system configurations for Kubernetes
     copy:
       dest: /etc/sysctl.d/kubernetes.conf
       content: |
         net.bridge.bridge-nf-call-ip6tables  = 1
         net.bridge.bridge-nf-call-iptables   = 1
         net.ipv4.ip_forward                  = 1
   ```

   - **Explanation**: Configures sysctl parameters required by Kubernetes networking.

7. **Apply Sysctl Parameters**

   ```yaml
   - name: Apply sysctl params
     command: sysctl --system
   ```

   - **Explanation**: Applies the sysctl settings immediately without reboot.

8. **Install Containerd Prerequisites**

   ```yaml
   - name: Install containerd prerequisites
     apt:
       name:
         - curl
         - gnupg2
         - software-properties-common
         - apt-transport-https
         - ca-certificates
         - socat
       state: present
   ```

   - **Explanation**: Installs necessary packages for setting up the container runtime and adding repositories.

9. **Install Containerd and Runc**

   ```yaml
   - name: Install containerd and runc
     apt:
       name:
         - containerd
         - runc
       state: present
   ```

   - **Explanation**: Installs `containerd` (container runtime) and `runc` (container runtime executor).

10. **Create Containerd Configuration Directory**

    ```yaml
    - name: Create containerd configuration directory
      file:
        path: /etc/containerd
        state: directory
    ```

    - **Explanation**: Ensures the `/etc/containerd` directory exists for configuration files.

11. **Generate Default Containerd Configuration**

    ```yaml
    - name: Generate default containerd configuration
      command: containerd config default > /etc/containerd/config.toml
      args:
        creates: /etc/containerd/config.toml
    ```

    - **Explanation**: Generates a default configuration file for `containerd` if it doesn't already exist.

12. **Set `SystemdCgroup` to True in Containerd Config**

    ```yaml
    - name: Set SystemdCgroup to true in containerd config
      replace:
        path: /etc/containerd/config.toml
        regexp: '^(\s*)SystemdCgroup = false'
        replace: '\1SystemdCgroup = true'
    ```

    - **Explanation**: Modifies the `containerd` configuration to use `systemd` as the cgroup driver, aligning with Kubernetes best practices.

13. **Restart and Enable Containerd Service**

    ```yaml
    - name: Restart and enable containerd service
      systemd:
        name: containerd.service
        state: restarted
        enabled: yes
    ```

    - **Explanation**: Restarts the `containerd` service to apply changes and enables it to start on boot.

14. **Add Kubernetes GPG Key**

    ```yaml
    - name: Add Kubernetes GPG key
      apt_key:
        url: https://pkgs.k8s.io/core:/stable:/v1.29/deb/Release.key
        state: present
        keyring: /etc/apt/keyrings/kubernetes-apt-keyring.gpg
    ```

    - **Explanation**: Adds the GPG key for the Kubernetes apt repository, ensuring package authenticity.

15. **Add Kubernetes Apt Repository**

    ```yaml
    - name: Add Kubernetes apt repository
      apt_repository:
        repo: "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.29/deb/ /"
        state: present
        filename: kubernetes
    ```

    - **Explanation**: Adds the Kubernetes apt repository to the system's package sources.

16. **Update Apt Cache After Adding Kubernetes Repo**

    ```yaml
    - name: Update apt cache after adding Kubernetes repo
      apt:
        update_cache: yes
    ```

    - **Explanation**: Updates the package lists to include packages from the newly added Kubernetes repository.

17. **Install Kubernetes Components**

    ```yaml
    - name: Install Kubernetes components
      apt:
        name:
          - kubelet
          - kubeadm
          - kubectl
        state: present
        allow_downgrade: yes
    ```

    - **Explanation**: Installs `kubelet`, `kubeadm`, and `kubectl`, which are essential for running and managing Kubernetes nodes.

18. **Hold Kubernetes Packages at Current Version**

    ```yaml
    - name: Hold Kubernetes packages at current version
      command: apt-mark hold kubelet kubeadm kubectl
    ```

    - **Explanation**: Prevents the Kubernetes packages from being automatically upgraded, which can lead to version mismatches in the cluster.

### Play 2: Initialize Kubernetes Control Plane Node

**Purpose**: Initialize the Kubernetes control plane on the master node and set up networking.

**Hosts**: `master`

**Variables**:

- `pod_network_cidr`: CIDR for the pod network (same as in Play 1).

#### Tasks:

1. **Initialize Kubernetes Cluster**

   ```yaml
   - name: Initialize Kubernetes cluster
     command: kubeadm init --pod-network-cidr={{ pod_network_cidr }}
     args:
       creates: /etc/kubernetes/admin.conf
     register: kubeadm_init
   ```

   - **Explanation**: Initializes the Kubernetes control plane using `kubeadm`.
   - **Note**: The `creates` parameter ensures the task runs only if `/etc/kubernetes/admin.conf` does not exist.
   - **Registers**: Captures the output in `kubeadm_init` for logging or debugging purposes.

2. **Create `.kube` Directory**

   ```yaml
   - name: Create .kube directory
     file:
       path: /home/{{ ansible_user }}/.kube
       state: directory
       owner: "{{ ansible_user }}"
       mode: 0755
   ```

   - **Explanation**: Creates the `.kube` directory in the home directory of the user running Ansible (`ansible_user`).

3. **Copy `admin.conf` to User's Kube Config**

   ```yaml
   - name: Copy admin.conf to user's kube config
     copy:
       src: /etc/kubernetes/admin.conf
       dest: /home/{{ ansible_user }}/.kube/config
       owner: "{{ ansible_user }}"
       mode: 0644
       remote_src: yes
   ```

   - **Explanation**: Copies the Kubernetes admin configuration file to the user's kubeconfig, allowing the user to use `kubectl`.

4. **Generate Kubeadm Join Command**

   ```yaml
   - name: Generate kubeadm join command
     command: kubeadm token create --print-join-command
     register: kubeadm_join_command
   ```

   - **Explanation**: Generates the `kubeadm join` command that worker nodes will use to join the cluster.
   - **Registers**: Stores the command in `kubeadm_join_command`.

5. **Set Kubeadm Join Command as Fact**

   ```yaml
   - name: Set kubeadm join command as fact
     set_fact:
       kubeadm_join_cmd: "{{ kubeadm_join_command.stdout }}"
   ```

   - **Explanation**: Sets the join command as a fact (`kubeadm_join_cmd`) to make it accessible to other plays and tasks.

6. **Install Calico Network Plugin**

```yaml
   - name: Install Calico network plugin
     shell: kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.25.0/manifests/calico.yaml
     environment:
       KUBECONFIG: /etc/kubernetes/admin.conf
```

   - **Explanation**: Applies the Calico manifest to set up networking for the cluster.
   - **Environment**: Uses the `KUBECONFIG` environment variable to authenticate with the cluster.

### Play 3: Join Worker Nodes to Cluster

**Purpose**: Joins the worker nodes to the Kubernetes cluster using the join command generated in Play 2.

**Hosts**: `workers`

#### Tasks:

1. **Retrieve Kubeadm Join Command from Master**

   ```yaml
   - name: Retrieve kubeadm join command from master
     set_fact:
       kubeadm_join_cmd: "{{ hostvars[groups['master'][0]]['kubeadm_join_cmd'] }}"
   ```

   - **Explanation**: Retrieves the `kubeadm_join_cmd` fact from the master node and sets it on the worker nodes.
   - **Note**: Uses `hostvars` and `groups` to access variables from another host.

2. **Join the Node to the Kubernetes Cluster**

   ```yaml
   - name: Join the node to the Kubernetes cluster
     command: "{{ kubeadm_join_cmd }}"
     args:
       creates: /etc/kubernetes/kubelet.conf
   ```

   - **Explanation**: Executes the join command on the worker nodes to add them to the cluster.
   - **Note**: The `creates` parameter ensures the task runs only if `/etc/kubernetes/kubelet.conf` does not exist (i.e., the node hasn't already joined).

---

## Conclusion

This Ansible playbook automates the complex process of setting up a Kubernetes cluster, ensuring that:

- All nodes are properly configured with the necessary system settings and components.
- The control plane is initialized correctly.
- Worker nodes join the cluster seamlessly.

By following this guide, you should have a clear understanding of each step involved in the playbook and the purpose behind each task.

**Next Steps**:

- Verify the cluster status using `kubectl get nodes` on the master node.
- Deploy applications to the cluster and test functionality.
- Customize the playbook further to suit specific environment needs (e.g., different network plugins, Kubernetes versions).