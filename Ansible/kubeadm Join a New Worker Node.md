## Understanding the `kubeadm join` Command

When you initially set up your cluster, `kubeadm` generates a join command that includes a token and a certificate hash. This command is used by worker nodes to securely join the cluster. However, the token has a default time-to-live (TTL) of 24 hours. After this period, the token expires, and you cannot use the old join command.

## Steps to Join a New Worker Node

Here are the steps to manually add a new worker node to your existing Kubernetes cluster:

### 1. Prepare the New Worker Node

First, you need to prepare the new worker node by performing the same setup tasks you did for the initial nodes, excluding the cluster initialization. This includes:

- Disabling swap.
- Updating sysctl settings.
- Installing container runtime (`containerd`).
- Installing Kubernetes components (`kubelet`, `kubeadm`, `kubectl`).

**Option 1: Using Your Ansible Playbook**

You can reuse the first play of your Ansible playbook (`Kubernetes Cluster Setup`) on the new worker node:

- Add the new worker node to your Ansible inventory under the `[workers]` group.
- Run the `Kubernetes Cluster Setup` play against this new node.

**Option 2: Manual Setup**

If you prefer to set up the node manually, perform all the tasks from the first play on the new worker node.

### 2. Generate a New `kubeadm join` Command on the Master Node

Since the original token may have expired, you need to generate a new `kubeadm join` command.

**On the Master Node:**

1. **Generate a New Token**

   ```bash
   sudo kubeadm token create
   ```

   This command outputs a new token. For example:

   ```
   abcdef.0123456789abcdef
   ```

2. **Get the CA Certificate Hash**

   You need the hash of the cluster's CA certificate. Run:

   ```bash
   openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | \
   openssl rsa -pubin -outform der 2>/dev/null | \
   openssl dgst -sha256 -hex | sed 's/^.* //'
   ```

   Alternatively, you can have `kubeadm` provide the full join command:

   ```bash
   kubeadm token create --print-join-command
   ```

   This outputs something like:

   ```
   kubeadm join <master_ip>:6443 --token abcdef.0123456789abcdef \
   --discovery-token-ca-cert-hash sha256:<hash>
   ```

   **Note**: Replace `<master_ip>` and `<hash>` with the actual IP address and hash provided.

### 3. Join the New Worker Node to the Cluster

**On the New Worker Node:**

Run the `kubeadm join` command generated in the previous step:

```bash
sudo kubeadm join <master_ip>:6443 --token abcdef.0123456789abcdef \
--discovery-token-ca-cert-hash sha256:<hash>
```

- Ensure you run this command as `root` or with `sudo`.
- Replace `<master_ip>`, `abcdef.0123456789abcdef`, and `<hash>` with the actual values.

### 4. Verify the Node Has Joined

**On the Master Node:**

Run:

```bash
kubectl get nodes
```

You should see the new worker node listed and, after a short while, its status should change to `Ready`.

## Automating the Process with Ansible

To streamline the process of adding new worker nodes in the future, you can modify your Ansible playbook or create a new play specifically for adding worker nodes.

### Modifying the Playbook

1. **Update Your Inventory File**

   Add the new worker node to the `[workers]` group in your inventory.

2. **Modify the Playbook to Regenerate the Join Command**

   In your `Initialize Kubernetes Control Plane Node` play, ensure that the task generating the `kubeadm join` command is adjusted to always create a new token when needed.

   ```yaml
   - name: Generate kubeadm join command
     command: kubeadm token create --print-join-command
     register: kubeadm_join_command
   ```

3. **Run the Playbook**

   Execute the playbook targeting the new worker node:

   ```bash
   ansible-playbook -i inventory.ini your_playbook.yml --limit new_worker_node_hostname
   ```

   This runs the playbook only on the new worker node and the master node.

### Creating a Separate Playbook for Adding Nodes

Alternatively, create a new Ansible playbook dedicated to adding new worker nodes.

**Example Playbook: `add_worker_node.yml`**

```yaml
- name: Prepare New Worker Node
  hosts: new_worker
  become: yes
  tasks:
    # Include all the setup tasks from the 'Kubernetes Cluster Setup' play
    # ...

- name: Generate Kubeadm Join Command
  hosts: master
  become: yes
  tasks:
    - name: Generate kubeadm join command
      command: kubeadm token create --print-join-command
      register: kubeadm_join_command
      run_once: true

    - name: Set kubeadm join command as fact
      set_fact:
        kubeadm_join_cmd: "{{ kubeadm_join_command.stdout }}"

- name: Join New Worker Node to Cluster
  hosts: new_worker
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

**Usage:**

1. Add the new worker node to your inventory under a new group `[new_worker]`.
2. Run the playbook:

   ```bash
   ansible-playbook -i inventory.ini add_worker_node.yml
   ```

## Best Practices

- **Token Expiry**: Be aware that tokens generated by `kubeadm` expire by default after 24 hours. You can adjust the TTL when generating a token:

  ```bash
  kubeadm token create --ttl 0
  ```

  Setting `--ttl 0` creates a token that never expires (not recommended for security reasons).

- **Secure the Token**: Treat the join token as a secret. Avoid exposing it unnecessarily.

- **Automate with Ansible**: Automating the addition of new nodes ensures consistency and saves time.

- **Regularly Update Your Cluster**: Keep your Kubernetes cluster components updated to the same version to avoid compatibility issues.

## Summary

To add a new worker node to your Kubernetes cluster:

1. **Prepare the Node**: Ensure it has all necessary configurations and installations.
2. **Generate a New Join Command**: On the master node, create a new token and obtain the `kubeadm join` command.
3. **Join the Node**: Run the join command on the new worker node.
4. **Verify**: Check the cluster status to confirm the node has joined successfully.

By following these steps or automating the process with Ansible, you can easily scale your Kubernetes cluster by adding more worker nodes as needed.

If you have any further questions or need clarification on any step, feel free to ask!