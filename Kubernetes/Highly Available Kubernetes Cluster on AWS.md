# **Guide: Setting Up a Highly Available Kubernetes Cluster on AWS with kubeadm**

---

## **Introduction**

This updated guide provides a step-by-step approach to setting up a highly available (HA) Kubernetes cluster on AWS EC2 instances using `kubeadm`. It incorporates the lessons learned from previous troubleshooting, ensuring that the pod network CIDR is correctly configured to prevent networking issues. You will prepare your nodes with Ansible, configure an AWS Network Load Balancer (NLB), initialize the cluster, join additional control-plane and worker nodes, and configure `kubectl` on your local machine (e.g., MacBook) to manage the cluster remotely.

---

## **Prerequisites**

- **AWS Account**: Access to create EC2 instances and configure AWS services.
- **EC2 Instances**:
  - **Control-plane Nodes**: Three Ubuntu 24.04 LTS instances.
  - **Worker Nodes**: Two Ubuntu 24.04 LTS instances.
- **Ansible Installed**: On your local machine or a management EC2 instance.
- **SSH Access**: Passwordless SSH access to all EC2 instances using SSH keys.
- **kubectl Installed**: On your local machine.
- **AWS Network Load Balancer**: Configured to distribute traffic to control-plane nodes.
- **Basic Understanding of Kubernetes, Ansible, and AWS Networking**.

---

## **Section 1: Preparing the Nodes with Ansible**

### **1.1 Setting Up the Ansible Inventory**

Create a file named `hosts.ini` to define your inventory:

```ini
[all]
controlplane1 ansible_host=<controlplane1_public_ip> ansible_user=ubuntu
controlplane2 ansible_host=<controlplane2_public_ip> ansible_user=ubuntu
controlplane3 ansible_host=<controlplane3_public_ip> ansible_user=ubuntu
worker1 ansible_host=<worker1_public_ip> ansible_user=ubuntu
worker2 ansible_host=<worker2_public_ip> ansible_user=ubuntu

[control_planes]
controlplane1
controlplane2
controlplane3

[workers]
worker1
worker2
```

- Replace `<controlplaneX_public_ip>` and `<workerX_public_ip>` with actual IP addresses.

### **1.2 Configuring Ansible**

Create an `ansible.cfg` file:

```ini
[defaults]
inventory = hosts.ini
remote_user = ubuntu
host_key_checking = False
private_key_file = /path/to/your/private/key.pem
```

- Replace `/path/to/your/private/key.pem` with the path to your SSH private key.

### **1.3 Writing the Ansible Playbook**

Create a playbook named `k8s_setup.yml`:

```yaml
---
- name: Prepare nodes for Kubernetes installation
  hosts: all
  become: yes
  vars:
    kubernetes_version: '1.30.6-00'  # Adjust to match the desired version
    pod_network_cidr: '10.244.0.0/16'  # Use Flannel's default network CIDR

  tasks:
    - name: Update and upgrade apt packages
      apt:
        update_cache: yes
        upgrade: yes

    - name: Disable swap
      command: swapoff -a
      when: ansible_swaptotal_mb > 0

    - name: Remove swap entry from fstab
      replace:
        path: /etc/fstab
        regexp: '^(\S+\s+\S+\s+swap\s+.*)$'
        replace: '#\1'

    - name: Load kernel modules
      shell: |
        modprobe overlay
        modprobe br_netfilter
      args:
        executable: /bin/bash

    - name: Ensure kernel modules are loaded on boot
      copy:
        dest: /etc/modules-load.d/k8s.conf
        content: |
          overlay
          br_netfilter

    - name: Set sysctl parameters
      copy:
        dest: /etc/sysctl.d/k8s.conf
        content: |
          net.bridge.bridge-nf-call-iptables  = 1
          net.bridge.bridge-nf-call-ip6tables = 1
          net.ipv4.ip_forward                 = 1

    - name: Apply sysctl parameters
      sysctl:
        reload: yes

    - name: Install containerd prerequisites
      apt:
        name:
          - apt-transport-https
          - ca-certificates
          - curl
        state: present

    - name: Install containerd
      apt:
        name: containerd
        state: present

    - name: Create containerd configuration directory
      file:
        path: /etc/containerd
        state: directory

    - name: Generate default containerd configuration
      command: >
        containerd config default | tee /etc/containerd/config.toml

    - name: Set SystemdCgroup to true in containerd config
      replace:
        path: /etc/containerd/config.toml
        regexp: 'SystemdCgroup = false'
        replace: 'SystemdCgroup = true'

    - name: Restart containerd
      systemd:
        name: containerd
        state: restarted
        enabled: yes

    - name: Add Kubernetes apt repository key
      apt_key:
        url: https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key
        state: present

    - name: Add Kubernetes apt repository
      apt_repository:
        repo: 'deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /'
        filename: 'kubernetes.list'
        state: present
        update_cache: yes

    - name: Install Kubernetes components
      apt:
        name:
          - kubelet={{ kubernetes_version }}
          - kubeadm={{ kubernetes_version }}
          - kubectl={{ kubernetes_version }}
        state: present
        allow_downgrade: yes

    - name: Hold Kubernetes packages at current version
      apt:
        name:
          - kubelet
          - kubeadm
          - kubectl
        state: hold

    - name: Enable kubelet service
      systemd:
        name: kubelet
        enabled: yes
        state: started
```

### **1.4 Running the Playbook**

Check connectivity between all the instances:

```Shell
ansible all -m ping
```

Execute the playbook:

```bash
ansible-playbook k8s_setup.yml
```

---

## **Section 2: Configuring the AWS Network Load Balancer**

### **2.1 Creating the NLB**

- **Create an NLB** named `k8s-api-nlb`.
- **Listeners**:
  - Protocol: TCP
  - Port: 6443
- **Target Group**:
  - Type: Instances
  - Protocol: TCP
  - Port: 6443
  - Add the three control-plane instances to the target group.

Creating NLB with Terraform:
```tf
# Network Load Balancer
resource "aws_lb" "k8s_api_nlb" {
  name               = "k8s-api-nlb"
  load_balancer_type = "network"
  subnets            = [aws_subnet.public.id]

  tags = {
    Name = "k8s-api-nlb"
  }
}

# Target Group for Control Plane Instances
resource "aws_lb_target_group" "k8s_api_tg" {
  name        = "k8s-api-tg"
  port        = 6443
  protocol    = "TCP"
  vpc_id      = aws_vpc.main.id
  target_type = "ip"

  health_check {
    protocol = "TCP"
    port     = "6443"
  }

  tags = {
    Name = "k8s-api-tg"
  }
}

# Attach Control Plane Instances to Target Group
resource "aws_lb_target_group_attachment" "k8s_api_tg_attachment" {
  for_each          = { for idx, instance in aws_instance.control_plane : idx => instance }
  target_group_arn  = aws_lb_target_group.k8s_api_tg.arn
  target_id         = each.value.private_ip
  port              = 6443
}

# Listener for the NLB
resource "aws_lb_listener" "k8s_api_listener" {
  load_balancer_arn = aws_lb.k8s_api_nlb.arn
  port              = 6443
  protocol          = "TCP"

  default_action {
    type             = "forward"
    target_group_arn = aws_lb_target_group.k8s_api_tg.arn
  }
}
```

### **2.2 Security Groups**

- **Control-plane Nodes**:
  - Allow inbound traffic on port 6443 from the NLB.
  - Allow inbound traffic from other nodes for inter-node communication.

---

## **Section 3: Initializing the Kubernetes Cluster**

### **3.1 SSH into the First Control-plane Node**

```bash
ssh ubuntu@<control-plane-1_public_ip>
```

### **3.2 Initialize the Control Plane**

Run the `kubeadm init` command with Flannel's default pod network CIDR:

```bash
sudo kubeadm init \
  --control-plane-endpoint "k8s-api-nlb-<unique-id>.elb.<region>.amazonaws.com:6443" \
  --upload-certs \
  --pod-network-cidr=10.244.0.0/16
```

- Replace `<unique-id>` and `<region>` with your NLB's DNS name and AWS region.

### **3.3 Set Up kubectl**

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/kubeadm-config
sudo chown $(id -u):$(id -g) $HOME/.kube/kubeadm-config
```

### **3.4 Set KUBECONFIG Environment Variable**

To make `kubectl` use the new config file:

```bash
export KUBECONFIG=$HOME/.kube/kubeadm-config
```

To make it permanent, add the export command to `~/.bashrc` or `~/.zshrc`:

```bash
echo 'export KUBECONFIG=$HOME/.kube/kubeadm-config' >> ~/.bashrc
source ~/.bashrc
```

### **3.5 Deploy Flannel Network Add-on**

Deploy Flannel using the default manifest:

```bash
kubectl apply -f https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml
```

**Note**: Since we used Flannel's default pod network CIDR (`10.244.0.0/16`), we do not need to modify the manifest.

---

## **Section 4: Joining Additional Control-plane Nodes**

### **4.1 Retrieve Join Command**

From the `kubeadm init` output, save the `kubeadm join` command for control-plane nodes, which includes the `--certificate-key`.

### **4.2 SSH into Each Additional Control-plane Node**

```bash
ssh ubuntu@<control-plane-2_public_ip>
```

### **4.3 Run the Join Command**

```bash
sudo kubeadm join k8s-api-nlb-<unique-id>.elb.<region>.amazonaws.com:6443 \
  --token <token> \
  --discovery-token-ca-cert-hash sha256:<hash> \
  --control-plane --certificate-key <certificate_key>
```

- Replace `<token>`, `<hash>`, and `<certificate_key>` with the values provided by `kubeadm init`.

Repeat for `control-plane-3`.

The `--token` parameter has some token that is generated by kubeadm. The token would be valid for up to 24 hours from its generation. You can check your token on the master where we initialized the cluster by running the below command.

```Shell
kubeadm token list
```

If the above command returns an empty output then your token has expired we need to create a new token by using the below command.

```Shell
kubeadm token create --print-join-command
```

>[!Note]
>The `--certificate-key` will cause the control plane certificates to be downloaded from the `kubeadm-certs` secret in the cluster and be decrypted using the given key.

---

## **Section 5: Joining Worker Nodes**

### **5.1 Retrieve Worker Join Command**

From the `kubeadm init` output, save the `kubeadm join` command for worker nodes.

### **5.2 SSH into Each Worker Node**

```bash
ssh ubuntu@<worker-1_public_ip>
```

### **5.3 Run the Join Command**

```bash
sudo kubeadm join k8s-api-nlb-<unique-id>.elb.<region>.amazonaws.com:6443 \
  --token <token> \
  --discovery-token-ca-cert-hash sha256:<hash>
```

Repeat for `worker-2`.

---

## **Section 6: Configuring kubectl on Your Local Machine**

### **6.1 Copy kubeconfig File from Control-plane Node**

On `control-plane-1`:

```bash
cat $HOME/.kube/kubeadm-config
```

Copy the content to your local machine.

### **6.2 Create kubeconfig File on Your Local Machine**

On your local machine:

```bash
mkdir -p $HOME/.kube
nano $HOME/.kube/kubeadm-config
```

- Paste the content.
- Replace the server's IP with the NLB DNS name in the kubeconfig file if needed:

  ```yaml
  server: https://k8s-api-nlb-<unique-id>.elb.<region>.amazonaws.com:6443
  ```

### **6.3 Set KUBECONFIG Environment Variable**

```bash
export KUBECONFIG=$HOME/.kube/kubeadm-config
```

Add to your shell configuration file (e.g., `~/.zshrc` or `~/.bash_profile`):

```bash
echo 'export KUBECONFIG=$HOME/.kube/kubeadm-config' >> ~/.zshrc
source ~/.zshrc
```

### **6.4 Adjust AWS Security Group**

Ensure your local machine's IP is allowed to access port 6443 on the NLB.

---

## **Section 7: Verifying the Cluster**

### **7.1 Check Nodes Status**

On your local machine or any control-plane node:

```bash
kubectl get nodes
```

**Expected Output**:

```
NAME               STATUS   ROLES           AGE     VERSION
controlplane1      Ready    control-plane   XXm     v1.30.6
controlplane2      Ready    control-plane   XXm     v1.30.6
controlplane3      Ready    control-plane   XXm     v1.30.6
worker1            Ready    <none>          XXm     v1.30.6
worker2            Ready    <none>          XXm     v1.30.6
```

### **7.2 Verify System Pods**

```bash
kubectl get pods -A
```

Ensure all pods, especially those in `kube-system`, are in the `Running` state.

---

## **Section 8: Additional Considerations**

### **8.1 Security**

- **Restrict Access**: Limit security group rules to specific IPs.
- **Use SSH Tunneling**: For secure access, consider SSH tunneling instead of exposing the API server publicly.

### **8.2 High Availability Testing**

- **Simulate Failures**: Stop `kube-apiserver` on one control-plane node to test failover.
- **Monitor NLB Health Checks**: Ensure the NLB routes traffic only to healthy nodes.

### **8.3 Regular Maintenance**

- **Updates**: Keep Kubernetes components updated.
- **Backups**: Regularly back up `etcd` data.
- **Monitoring**: Implement monitoring with tools like Prometheus and Grafana.

---

## **Troubleshooting Tips**

### **Issue: Flannel Pods Not Running**

- **Symptom**: Flannel pods are in `CrashLoopBackOff` or `Error` state.
- **Cause**: Pod network CIDR mismatch.
- **Solution**: Ensure that the `--pod-network-cidr` flag during `kubeadm init` matches Flannel's default CIDR (`10.244.0.0/16`).

### **Issue: CoreDNS Pods Stuck in `ContainerCreating`**

- **Symptom**: CoreDNS pods are not running.
- **Cause**: Underlying network issues, often related to the CNI plugin.
- **Solution**: Fix Flannel deployment as above; ensure networking is functioning.

### **Issue: Nodes Not Ready**

- **Symptom**: Nodes show `NotReady` status.
- **Cause**: Networking issues, kubelet not running, or other configuration problems.
- **Solution**: Verify that all required services are running and that the network plugin is correctly deployed.

---

## **Conclusion**

By following this updated guide, you've set up a highly available Kubernetes cluster on AWS using `kubeadm`. The guide ensures that the pod network CIDR matches between `kubeadm init` and Flannel's default configuration, preventing the networking issues encountered previously.

---
## **Resources**

- [# Setup kubernetes HA cluster in AWS using kubeadm](https://medium.com/@murtazavasi.dev/setup-kubernetes-ha-cluster-in-aws-using-kubeadm-part-2-the-how-b7614166af71)
