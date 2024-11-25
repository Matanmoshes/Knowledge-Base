Certainly! I've updated the guide to include the steps to install the `nvme_tcp` kernel module on your worker nodes. This will ensure that all necessary kernel modules are properly installed and loaded. Here's the complete, updated guide:

---

## **Table of Contents**

1. [Prerequisites](https://chatgpt.com/c/673ce6b6-d744-8006-ad03-9c34f4718ee8#1-prerequisites)
2. [Cluster Setup with Terraform](https://chatgpt.com/c/673ce6b6-d744-8006-ad03-9c34f4718ee8#2-cluster-setup-with-terraform)
3. [Prepare Worker Nodes](https://chatgpt.com/c/673ce6b6-d744-8006-ad03-9c34f4718ee8#3-prepare-worker-nodes)
    - 3.1 [Verify Kernel Version and Modules](https://chatgpt.com/c/673ce6b6-d744-8006-ad03-9c34f4718ee8#31-verify-kernel-version-and-modules)
    - 3.2 [Install Necessary Packages](https://chatgpt.com/c/673ce6b6-d744-8006-ad03-9c34f4718ee8#32-install-necessary-packages)
    - 3.3 [Configure HugePages](https://chatgpt.com/c/673ce6b6-d744-8006-ad03-9c34f4718ee8#33-configure-hugepages)
4. [Verify EBS Volumes on Worker Nodes](https://chatgpt.com/c/673ce6b6-d744-8006-ad03-9c34f4718ee8#4-verify-ebs-volumes-on-worker-nodes)
5. [Install Helm on Control Plane Node](https://chatgpt.com/c/673ce6b6-d744-8006-ad03-9c34f4718ee8#5-install-helm-on-control-plane-node)
6. [Add OpenEBS Helm Repository](https://chatgpt.com/c/673ce6b6-d744-8006-ad03-9c34f4718ee8#6-add-openebs-helm-repository)
7. [Install OpenEBS with Replicated Storage](https://chatgpt.com/c/673ce6b6-d744-8006-ad03-9c34f4718ee8#7-install-openebs-with-replicated-storage)
8. [Configure Storage Pools](https://chatgpt.com/c/673ce6b6-d744-8006-ad03-9c34f4718ee8#8-configure-storage-pools)
9. [Create StorageClass with Replication Factor 3](https://chatgpt.com/c/673ce6b6-d744-8006-ad03-9c34f4718ee8#9-create-storageclass-with-replication-factor-3)
10. [Test OpenEBS Replicated Volume](https://chatgpt.com/c/673ce6b6-d744-8006-ad03-9c34f4718ee8#10-test-openebs-replicated-volume)
11. [Troubleshooting and Verification](https://chatgpt.com/c/673ce6b6-d744-8006-ad03-9c34f4718ee8#11-troubleshooting-and-verification)
12. [Next Steps and Best Practices](https://chatgpt.com/c/673ce6b6-d744-8006-ad03-9c34f4718ee8#12-next-steps-and-best-practices)

---

## **1. Prerequisites**

Before starting, ensure you have the following:

- **AWS EC2 Instances**:
    - One control plane node.
    - Three worker nodes.
- **Ubuntu OS** on all nodes.
- **EBS Volumes** attached to each worker node (via Terraform).
- **kubectl** and **kubeadm** installed.
- **Helm 3.7+** installed on the control plane node.
- **Terraform** scripts to create and manage your cluster.

---

## **2. Cluster Setup with Terraform**

Use your Terraform scripts to create the Kubernetes cluster and attach EBS volumes to each worker node. Ensure that:

- The cluster is up and running.
- Each worker node has an unformatted and unpartitioned EBS volume attached.

---

## **3. Prepare Worker Nodes**

Perform the following steps on **each worker node**.

### **3.1 Verify Kernel Version and Modules**

**a. Check Kernel Version**

```bash
uname -r
```

- Ensure the kernel version is **5.13** or higher.
- If the kernel version is lower, upgrade the kernel.

**b. Install `nvme_tcp` Kernel Module**

To ensure the `nvme_tcp` kernel module is available and loaded, perform the following steps.

**i. List NVMe Host Modules**

```bash
ls /lib/modules/$(uname -r)/kernel/drivers/nvme/host/
```

- This command checks if the `nvme_tcp.ko` module is present.

**ii. Install Kernel Extra Modules**

If `nvme_tcp.ko` is not present, install the extra kernel modules package:

```bash
sudo apt update -y
sudo apt install linux-modules-extra-$(uname -r) -y
```

**iii. Load the `nvme_tcp` Module**

```bash
sudo modprobe nvme_tcp
```

**iv. Verify the Module is Loaded**

```bash
lsmod | grep nvme_tcp
```

- You should see output indicating that `nvme_tcp` is loaded.

**v. Ensure the Module Loads on Boot**

```bash
echo "nvme_tcp" | sudo tee -a /etc/modules
```

**c. Verify CPU Supports SSE4.2**

```bash
grep sse4_2 /proc/cpuinfo
```

- Ensure the output includes `sse4_2`.

**d. Verify `ext4` Filesystem Support**

The `ext4` module is typically built into the kernel. Verify it:

```bash
lsmod | grep ext4
```

- If not present, load it:
    
    ```bash
    sudo modprobe ext4
    ```
    

---

### **3.2 Install Necessary Packages**

Install required packages:

```bash
sudo apt update -y
sudo apt install -y lsscsi nvme-cli
```

---

### **3.3 Configure HugePages**

**a. Check Current HugePages Configuration**

```bash
grep HugePages /proc/meminfo
```

**b. Set HugePages to 1024**

```bash
echo 'vm.nr_hugepages=1024' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

**c. Verify HugePages**

```bash
grep HugePages /proc/meminfo
```

- You should see:
    
    ```
    HugePages_Total:    1024
    HugePages_Free:     1024
    ```
    

**d. Restart Kubelet**

```bash
sudo systemctl restart kubelet
```

---

## **4. Verify EBS Volumes on Worker Nodes**

Perform the following steps on **each worker node**.

### **4.1 Verify Attached EBS Volumes**

**a. List Block Devices**

```bash
lsblk
```

**Expected Output:**

You should see an additional unformatted disk (e.g., `/dev/nvme1n1`):

```plaintext
NAME         MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
nvme0n1      259:0    0     8G  0 disk
├─nvme0n1p1  259:1    0     7G  0 part /
├─nvme0n1p14 259:2    0     4M  0 part
├─nvme0n1p15 259:3    0   106M  0 part /boot/efi
└─nvme0n1p16 259:4    0   913M  0 part /boot
nvme1n1      259:5    0    20G  0 disk   # This is the new EBS volume
```

**b. Ensure the New Disk is Unformatted and Unpartitioned**

```bash
sudo fdisk -l /dev/nvme1n1
```

- The output should indicate that `/dev/nvme1n1` does not contain a valid partition table.

**Repeat these steps for all three worker nodes.**

---

## **5. Install Helm on Control Plane Node**

Perform the following steps on the **control plane node**.

### **5.1 Install Helm**

**a. Check if Helm is Installed**

```bash
helm version --short
```

- If Helm is not installed or the version is below 3.7, install it:
    
```bash
curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash
```
    

---

## **6. Add OpenEBS Helm Repository**

Perform the following steps on the **control plane node**.

### **6.1 Add OpenEBS Repo**

```bash
helm repo add openebs https://openebs.github.io/openebs
```

### **6.2 Update Helm Repositories**

```bash
helm repo update
```

---

## **7. Install OpenEBS with Replicated Storage**

Perform the following steps on the **control plane node**.

### **7.1 Label Worker Nodes for OpenEBS**

**a. List Nodes**

```bash
kubectl get nodes
```

**b. Label Nodes**

```bash
kubectl label node worker-node-1 openebs.io/engine=mayastor
kubectl label node worker-node-2 openebs.io/engine=mayastor
kubectl label node worker-node-3 openebs.io/engine=mayastor
```

### **7.2 Create Custom `values.yaml` for Helm**

Create a file named `openebs-values.yaml`:

```yaml
engines:
  replicated:
    mayastor:
      enabled: true
mayastorControlPlane:
  defaultReplicas: 3
```

### **7.3 Install OpenEBS Using Helm**

```bash
helm install openebs --namespace openebs openebs/openebs --create-namespace -f openebs-values.yaml
```

---

## **8. Configure Storage Pools**

### **8.1 Identify Available Disks**

**Perform on:** **All Worker Nodes**

- **List Block Devices**
    
    ```bash
    lsblk
    ```
    
- **Identify the New Disk**
    
    - The new disk should be unpartitioned and unformatted (e.g., `/dev/nvme1n1`).

### **8.2 Verify Block Devices with NDM**

**Perform on:** **Control Plane Node**

- **List BlockDevices Discovered by NDM**
    
    ```bash
    kubectl get blockdevices -n openebs
    ```
    
- **Sample Output**
    
    ```plaintext
    NAME                                           NODENAME        SIZE          STATE   TYPE       FSTYPE   AGE
    blockdevice-1234567890abcdef                   worker-node-1   20GiB         Active  BlockDevice          5m
    blockdevice-abcdef1234567890                   worker-node-2   20GiB         Active  BlockDevice          5m
    blockdevice-0fedcba987654321                   worker-node-3   20GiB         Active  BlockDevice          5m
    ```
    

### **8.3 Label BlockDevices**

**Perform on:** **Control Plane Node**

- **Label Each BlockDevice**
    
    ```bash
    kubectl label blockdevice -n openebs <blockdevice-name> openebs.io/engine=mayastor
    ```
    
- **Example**
    
    ```bash
    kubectl label blockdevice -n openebs blockdevice-1234567890abcdef openebs.io/engine=mayastor
    kubectl label blockdevice -n openebs blockdevice-abcdef1234567890 openebs.io/engine=mayastor
    kubectl label blockdevice -n openebs blockdevice-0fedcba987654321 openebs.io/engine=mayastor
    ```
    

### **8.4 Create DiskPool CRD**

**Perform on:** **Control Plane Node**

- **Create `diskpool.yaml`**
    
    ```yaml
    apiVersion: mayastor.io/v1alpha1
    kind: DiskPool
    metadata:
      name: mayastor-diskpool
      namespace: openebs
    spec:
      nodeSelector:
        openebs.io/engine: mayastor
      disks:
        - /dev/nvme1n1   # Replace with the correct disk if different
    ```
    
- **Apply the DiskPool Configuration**
    
    ```bash
    kubectl apply -f diskpool.yaml
    ```
    
- **Verify Storage Pools**
    
    ```bash
    kubectl get sp -n openebs
    ```
    
    - You should see storage pools created for each worker node.

---

## **9. Create StorageClass with Replication Factor 3**

**Perform on:** **Control Plane Node**

### **9.1 Create `mayastor-sc.yaml`**

Create a YAML file named `mayastor-sc.yaml`:

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: mayastor-repl3
provisioner: io.openebs.csi-mayastor
allowVolumeExpansion: true
parameters:
  repl: "3"
  protocol: "nvmf"
volumeBindingMode: Immediate
```

### **9.2 Apply the StorageClass**

```bash
kubectl apply -f mayastor-sc.yaml
```

---

## **10. Test OpenEBS Replicated Volume**

**Perform on:** **Control Plane Node**

### **10.1 Create a PVC**

Create a file named `mayastor-pvc.yaml`:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mayastor-pvc
spec:
  storageClassName: mayastor-repl3
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 2Gi
```

Apply the PVC:

```bash
kubectl apply -f mayastor-pvc.yaml
```

Verify the PVC is **Bound**:

```bash
kubectl get pvc
```

### **10.2 Create a Test Pod**

Create a file named `mayastor-test-pod.yaml`:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mayastor-test-pod
spec:
  containers:
  - name: test-container
    image: busybox
    command: ['sh', '-c', 'echo "Hello OpenEBS Mayastor" > /mnt/store/hello.txt && sleep 3600']
    volumeMounts:
    - mountPath: "/mnt/store"
      name: mayastor-storage
  volumes:
  - name: mayastor-storage
    persistentVolumeClaim:
      claimName: mayastor-pvc
```

Apply the Pod:

```bash
kubectl apply -f mayastor-test-pod.yaml
```

Verify the Pod is **Running**:

```bash
kubectl get pods
```

### **10.3 Verify Data Replication**

**a. Access the Pod**

```bash
kubectl exec -it mayastor-test-pod -- sh
```

**b. Verify the File**

Inside the pod shell, run:

```bash
cat /mnt/store/hello.txt
```

You should see:

```
Hello OpenEBS Mayastor
```

**c. Exit the Pod**

```bash
exit
```

### **10.4 Simulate Node Failure**

**a. Cordoning and Draining a Node**

Cordoning `worker-node-1`:

```bash
kubectl cordon worker-node-1
```

Draining `worker-node-1`:

```bash
kubectl drain worker-node-1 --ignore-daemonsets --delete-local-data
```

**b. Verify Pod Rescheduling**

Check if the pod has been rescheduled to another node:

```bash
kubectl get pods -o wide
```

**c. Verify Data Persistence**

Access the pod again and verify the file is still present:

```bash
kubectl exec -it mayastor-test-pod -- sh
cat /mnt/store/hello.txt
exit
```

**d. Uncordon the Node**

After testing, uncordon the node:

```bash
kubectl uncordon worker-node-1
```

---

## **11. Troubleshooting and Verification**

### **11.1 Verify OpenEBS Components**

**Perform on:** **Control Plane Node**

- List all pods in the `openebs` namespace:
    
    ```bash
    kubectl get pods -n openebs
    ```
    
- Ensure that all OpenEBS components are **Running**.
    

### **11.2 Check Mayastor Components**

- List Mayastor `io-engine` pods:
    
    ```bash
    kubectl get pods -n openebs -l app=io-engine
    ```
    
- Ensure that `io-engine` pods are running on all worker nodes.
    

### **11.3 Verify Storage Pools and Volumes**

**a. List Storage Pools**

```bash
kubectl get sp -n openebs
```

**b. Describe a Storage Pool**

```bash
kubectl describe sp <storage-pool-name> -n openebs
```

**c. List Volumes**

```bash
kubectl get volumes -n openebs
```

**d. Describe a Volume**

```bash
kubectl describe volume <volume-name> -n openebs
```

### **11.4 Common Issues**

- **Pods Not Running**: Check pod logs.
    
    ```bash
    kubectl logs <pod-name> -n openebs
    ```
    
- **PVC Not Bound**: Ensure the StorageClass is correctly configured and there are sufficient resources.
    
    ```bash
    kubectl describe pvc <pvc-name>
    ```
    
- **HugePages Misconfiguration**: Ensure HugePages are correctly set on all worker nodes and that kubelet was restarted.
    
- **Kernel Modules Not Loaded**: Verify that the `nvme_tcp` module is loaded on all worker nodes.
    
    ```bash
    lsmod | grep nvme_tcp
    ```
    
- **Firewall Ports**: Ensure required ports are open between nodes (e.g., 10124, 8420, 4421).
    

---

## **12. Next Steps and Best Practices**

### **12.1 Monitoring OpenEBS**

Consider setting up monitoring using Prometheus and Grafana.

**a. Install Prometheus Operator**

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install prometheus-operator prometheus-community/kube-prometheus-stack
```

**b. Configure OpenEBS Exporter**

Follow OpenEBS documentation to set up exporters for monitoring.

### **12.2 Backup and Disaster Recovery**

Implement backup solutions like Velero.

```bash
helm repo add vmware-tanzu https://vmware-tanzu.github.io/helm-charts
helm repo update
helm install velero vmware-tanzu/velero --namespace velero --create-namespace
```

### **12.3 Performance Tuning**

- **Network Performance**: Ensure low latency and high bandwidth between nodes.
- **Resource Allocation**: Allocate sufficient CPU and memory to OpenEBS components.

### **12.4 Regular Maintenance**

- **Update OpenEBS**: Keep OpenEBS components updated.
- **Node Maintenance**: Plan for node maintenance, ensuring data redundancy is maintained.

### **12.5 Documentation and Support**

- **OpenEBS Documentation**: [OpenEBS Official Docs](https://openebs.io/docs)
- **Community Support**: Join the OpenEBS community on Slack or forums.

---

## **Conclusion**

By following this updated guide, you've set up OpenEBS with replicated storage on your Kubernetes cluster with one control plane and three worker nodes, each with an attached EBS volume. The inclusion of the `nvme_tcp` kernel module installation ensures that all necessary kernel modules are properly loaded on your worker nodes. You've ensured data redundancy and high availability across your cluster.

---

## Reference:

- https://openebs.io/docs/quickstart-guide/installation