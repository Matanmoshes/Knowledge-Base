## **Introduction**

This guide provides a concise walkthrough to configure `kubectl` on your MacBook, allowing you to manage a Kubernetes cluster initialized with `kubeadm` on an AWS EC2 instance. The steps reflect the successful configuration you've achieved, focusing on key actions to establish connectivity between your local machine and the remote cluster.

---

## **Prerequisites**

- **Kubernetes Cluster**: Initialized on an AWS EC2 instance using `kubeadm`.
- **Public and Private IP Addresses**:
  - **Public IP** of EC2 instance: `44.201.23.217`
  - **Private IP** of EC2 instance: `10.0.1.140`
- **kubectl Installed**: Ensure `kubectl` is installed on your MacBook:
  
  ```bash
  brew install kubectl
  ```
  
- **SSH Access**: Ability to SSH into your EC2 instance with appropriate permissions.

---

## **Steps on the EC2 Instance**

### **1. Initialize the Kubernetes Cluster**

Run the following command on your EC2 instance to initialize the cluster, including both the private and public IP addresses:

```bash
sudo kubeadm init \
  --pod-network-cidr=10.244.0.0/16 \
  --apiserver-advertise-address=10.0.1.140 \
  --apiserver-cert-extra-sans=44.201.23.217
```

**Explanation:**

- `--pod-network-cidr=10.244.0.0/16`: Specifies the CIDR for the pod network (compatible with Flannel).
- `--apiserver-advertise-address=10.0.1.140`: The private IP address the API server advertises to other nodes.
- `--apiserver-cert-extra-sans=44.201.23.217`: Adds the public IP to the API server's certificate, allowing external access.

### **2. Deploy a Pod Network Add-on**

Apply a pod network add-on so that your pods can communicate with each other. For Flannel:

```bash
kubectl apply -f https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml
```

---

## **Steps on Your MacBook**

### **1. Obtain the kubeconfig File from the EC2 Instance**

Copy the Kubernetes admin configuration file from the EC2 instance to your MacBook.

**On the EC2 Instance:**

- Display the content of the kubeconfig file:

  ```bash
  cat $HOME/.kube/config
  ```

- Copy the output and save it to a file on your MacBook.

**On Your MacBook:**

- Create a directory for your Kubernetes configuration if it doesn't exist:

  ```bash
  mkdir -p $HOME/.kube
  ```

- Create a new file named `kubeadm-config-v2` in the `.kube` directory:

  ```bash
  vi $HOME/.kube/kubeadm-config-v2
  ```

- Paste the content copied from the EC2 instance into this file.

### **2. Modify the kubeconfig File**

Edit the kubeconfig file to replace the private IP with the public IP.

- Open the file:

  ```bash
  vi $HOME/.kube/kubeadm-config-v2
  ```

- Locate the `server` field under the `clusters` section:

  ```yaml
  server: https://10.0.1.140:6443
  ```

- Change it to the public IP:

  ```yaml
  server: https://44.201.23.217:6443
  ```

- Save and exit the editor.

### **3. Set the KUBECONFIG Environment Variable**

Tell `kubectl` to use the modified configuration file.

- Run:

  ```bash
  export KUBECONFIG=$HOME/.kube/kubeadm-config-v2
  ```

- To make this setting persistent across sessions, add the export command to your shell profile (`~/.bash_profile`, `~/.zshrc`, etc.):

  ```bash
  echo 'export KUBECONFIG=$HOME/.kube/kubeadm-config-v2' >> ~/.zshrc
  source ~/.zshrc
  ```

### **4. Adjust AWS Security Group Settings**

Ensure the security group associated with your EC2 instance allows inbound traffic on port **6443** from your MacBook's public IP.

**Steps:**

- **Find Your MacBook's Public IP**:

  ```bash
  curl ifconfig.me
  ```

- **Modify Security Group Inbound Rules**:

  - Log in to the AWS Management Console.
  - Navigate to **EC2** > **Security Groups**.
  - Select the security group attached to your EC2 instance.
  - **Edit Inbound Rules**:
    - **Type**: Custom TCP
    - **Protocol**: TCP
    - **Port Range**: 6443
    - **Source**: Your MacBook's public IP with `/32` (e.g., `203.0.113.25/32`)
    - **Description**: Allow kubectl access from MacBook
  - **Save** the changes.

### **5. Test the Connection**

Verify that `kubectl` can communicate with your Kubernetes cluster.

- Run:

  ```bash
  kubectl get nodes
  ```

- **Expected Output**:

  ```
  NAME            STATUS   ROLES           AGE   VERSION
  ip-10-0-1-140   Ready    control-plane   XXm   v1.xx.x
  ```

---

## **Summary**

You've successfully configured `kubectl` on your MacBook to access your remote Kubernetes cluster on AWS EC2 by:

1. Initializing the cluster with both private and public IP addresses.
2. Copying and modifying the kubeconfig file to point to the public IP.
3. Setting the `KUBECONFIG` environment variable to use the custom configuration file.
4. Adjusting the AWS security group to allow inbound traffic on port 6443 from your MacBook.
5. Testing the setup to ensure connectivity.

---

## **Additional Tips**

- **Security Considerations**:

  - **Restrict Access**: Limit the security group rule to your specific IP address.
  - **Regular Updates**: If your public IP changes frequently, consider using a dynamic DNS service or regularly update the security group.

- **Make KUBECONFIG Permanent**:

  - If you haven't already, add the export command to your shell profile to avoid setting it every time.

- **Verify kube-apiserver Binding**:

  - Ensure the API server is configured to listen on all interfaces by checking the `--bind-address=0.0.0.0` flag in `/etc/kubernetes/manifests/kube-apiserver.yaml` on the EC2 instance.

- **Use SSH Tunneling for Enhanced Security (Optional)**:

  - Instead of exposing the API server, you can use SSH tunneling:

    ```bash
    ssh -i /path/to/private-key.pem -L 6443:localhost:6443 ubuntu@44.201.23.217
    ```

  - Modify your kubeconfig to point to `https://localhost:6443`.

---

## **Troubleshooting**

- **Cannot Reach the API Server**:

  - Double-check the security group settings.
  - Ensure there are no local firewalls blocking outbound traffic on port 6443.

- **kubectl Commands Hang or Fail**:

  - Verify the `server` field in your kubeconfig is set to the correct public IP.
  - Make sure the `KUBECONFIG` environment variable is set correctly.

- **Authentication Errors**:

  - Ensure the certificate data in your kubeconfig file is correct and hasn't been corrupted during copying.



