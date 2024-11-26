# Setting Up OpenEBS Replicated Persistent Volumes with Mayastor on Kubernetes

This comprehensive guide will walk you through the process of setting up **OpenEBS** with **Mayastor** for replicated Persistent Volumes (PVs) on your Kubernetes cluster. By following these steps, you'll achieve a robust, high-availability storage solution leveraging AWS EBS volumes.


---

## 1. Prerequisites

Before starting, ensure you have the following:

- **AWS EC2 Instances**:
    - **One Control Plane Node**
    - **Three Worker Nodes**
- **Ubuntu OS** installed on all nodes.
- **EBS Volumes** attached to each worker node (via Terraform).
- **kubectl** and **kubeadm** installed and configured.
- **Helm 3.7+** installed on the control plane node.
- **Terraform** scripts to create and manage your cluster.
- **Sufficient Privileges** to install and configure Kubernetes components.

---

## 2. Cluster Setup with Terraform

Use your existing Terraform scripts to create the Kubernetes cluster and attach EBS volumes to each worker node. Ensure that:

- The Kubernetes cluster is up and running.
- Each worker node has an **unformatted** and **unpartitioned** EBS volume attached.

**Note:** This guide assumes that the Terraform setup has been successfully executed and the cluster is operational with the specified nodes and EBS volumes.

---

## 3. Prepare Worker Nodes

Perform the following steps on **each worker node** (`ip-10-0-1-119`, `ip-10-0-1-252`, `ip-10-0-1-61`).

### 3.1 Verify Kernel Version and Modules

#### a. Check Kernel Version

Ensure your worker nodes are running **kernel version 5.13** or higher.

```bash
uname -r
```

- **Expected Output:** Kernel version **5.13** or newer.
    
- **If the kernel version is lower**, upgrade the kernel:
    
    ```bash
    sudo apt update -y
    sudo apt upgrade -y
    sudo reboot
    ```
    

#### b. Install `nvme_tcp` Kernel Module

To ensure the `nvme_tcp` kernel module is available and loaded, follow these steps.

##### i. List NVMe Host Modules

```bash
ls /lib/modules/$(uname -r)/kernel/drivers/nvme/host/
```

- **Expected Output:** Presence of `nvme_tcp.ko`.
    
    If `nvme_tcp.ko` is **not present**, proceed to install the extra kernel modules.
    

##### ii. Install Kernel Extra Modules

```bash
sudo apt update -y
sudo apt install linux-modules-extra-$(uname -r) -y
```

##### iii. Load the `nvme_tcp` Module

```bash
sudo modprobe nvme_tcp
```

##### iv. Verify the Module is Loaded

```bash
lsmod | grep nvme_tcp
```

- **Expected Output:** Line indicating `nvme_tcp` is loaded.

##### v. Ensure the Module Loads on Boot

```bash
echo "nvme_tcp" | sudo tee -a /etc/modules
```

#### c. Verify CPU Supports SSE4.2

Ensure that your CPUs support the SSE4.2 instruction set.

```bash
grep sse4_2 /proc/cpuinfo
```

- **Expected Output:** Presence of `sse4_2` in the flags.

#### d. Verify `ext4` Filesystem Support

The `ext4` module should be available.

```bash
lsmod | grep ext4
```

Checking Available Filesystems:

```Shell
cat /proc/filesystems
```

- **If `ext4` not present**, load it:
    
    ```bash
    sudo modprobe ext4
    ```
    

### 3.2 Install Necessary Packages

Install required packages for disk management and NVMe.

```bash
sudo apt update -y
sudo apt install -y lsscsi nvme-cli
```

### 3.3 Configure HugePages

HugePages are essential for high-performance storage operations.

#### a. Check Current HugePages Configuration

```bash
grep HugePages /proc/meminfo
```

#### b. Set HugePages to 1024

```bash
echo 'vm.nr_hugepages=1024' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

#### c. Verify HugePages

```bash
grep HugePages /proc/meminfo
```

- **Expected Output:**
    
    ```
    HugePages_Total:    1024
    HugePages_Free:     1024
    ```
    

#### d. Restart Kubelet

```bash
sudo systemctl restart kubelet
```

---

## 4. Verify EBS Volumes on Worker Nodes

Ensure that each worker node has an attached, unformatted, and unpartitioned EBS volume.

### 4.1 Verify Attached EBS Volumes

```bash
lsblk
```

- **Expected Output:** An additional unformatted disk (e.g., `/dev/nvme1n1`).
    
    ```plaintext
    NAME         MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
    nvme0n1      259:0    0     8G  0 disk
    ├─nvme0n1p1  259:1    0     7G  0 part /
    ├─nvme0n1p14 259:2    0     4M  0 part
    ├─nvme0n1p15 259:3    0   106M  0 part /boot/efi
    └─nvme0n1p16 259:4    0   913M  0 part /boot
    nvme1n1      259:5    0    20G  0 disk   # This is the new EBS volume
    ```
    

### 4.2 Ensure the New Disk is Unformatted and Unpartitioned

```bash
sudo fdisk -l /dev/nvme1n1
```

- **Expected Output:** Indication that `/dev/nvme1n1` does not contain a valid partition table.

**Repeat these steps for all three worker nodes.**

---

## 5. Install Helm on Control Plane Node

Perform the following steps on the **control plane node**.

List the nodes:

```Shell
kubectl get nodes
```

Label all the worker nodes:

```Shell
kubectl label node worker-node-1 openebs.io/engine=mayastor
kubectl label node worker-node-2 openebs.io/engine=mayastor
kubectl label node worker-node-3 openebs.io/engine=mayastor
```
### 5.1 Install Helm

#### a. Check if Helm is Installed

```bash
helm version --short
```

- **If Helm is not installed** or the version is below **3.7**, install it:
    
```bash
curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash
```
    
- **Verify Installation:**
    
```bash
helm version --short
```

---

## 6. Install OpenEBS with Mayastor

OpenEBS with Mayastor provides high-performance, replicated NVMe storage for Kubernetes.

### 6.1 Add OpenEBS Helm Repository

```bash
helm repo add openebs https://openebs.github.io/charts
helm repo update
```

### 6.2 Install OpenEBS

```bash
helm install openebs openebs/openebs --namespace openebs --create-namespace
```

- **Verify Installation:**

```bash
helm ls -n openebs
kubectl get pods -n openebs
```
    
    **Expected Output:** All OpenEBS pods should be in the `Running` state.
    

### 6.3 Install Mayastor

**Note:** Mayastor is a high-performance, cloud-native storage engine for OpenEBS. Ensure that you follow the [Mayastor Installation Guide](https://mayastor.io/docs/) for the latest and most accurate instructions. Below is a simplified installation example.

#### a. Apply Mayastor Operator

```bash
kubectl apply -f https://raw.githubusercontent.com/openebs/Mayastor/master/deploy/mayastor-operator.yaml
```

#### b. Verify Mayastor Pods

```bash
kubectl get pods -n mayastor
```

- **Expected Output:** All Mayastor pods should be in the `Running` state.

---

## 7. Create DiskPools on Worker Nodes

Define DiskPools on each worker node to utilize the attached EBS volumes.

Run this command on all the worker node to find out the disk link:

```Shell
ls -l /dev/disk/by-id/ | grep nvme
lrwxrwxrwx 1 root root 13 Nov 25 16:42 nvme-Amazon_Elastic_Block_Store_vol083f7398414064f9b -> ../../nvme1n1
```

### 7.1 Create DiskPool Definitions

Run the following commands on the **control plane node** to create DiskPools for each worker node.

#### a. DiskPool for `ip-10-0-1-61`

```bash
cat <<EOF | kubectl apply -f -
apiVersion: openebs.io/v1beta2
kind: DiskPool
metadata:
  name: pool-on-node-1
  namespace: openebs
spec:
  node: ip-10-0-1-61
  disks:
    - uring:///dev/disk/by-id/nvme-Amazon_Elastic_Block_Store_vol083f7398414064f9b
EOF
```

**Expected Output:**

```
diskpool.openebs.io/pool-on-node-1 created
```

#### b. DiskPool for `ip-10-0-1-252`

```bash
cat <<EOF | kubectl apply -f -
apiVersion: openebs.io/v1beta2
kind: DiskPool
metadata:
  name: pool-on-node-2
  namespace: openebs
spec:
  node: ip-10-0-1-252
  disks:
    - uring:///dev/disk/by-id/nvme-Amazon_Elastic_Block_Store_vol01733245c3f755a48
EOF
```

**Expected Output:**

```
diskpool.openebs.io/pool-on-node-2 created
```

#### c. DiskPool for `ip-10-0-1-119`

```bash
cat <<EOF | kubectl apply -f -
apiVersion: openebs.io/v1beta2
kind: DiskPool
metadata:
  name: pool-on-node-3
  namespace: openebs
spec:
  node: ip-10-0-1-119
  disks:
    - uring:///dev/disk/by-id/nvme-Amazon_Elastic_Block_Store_vol0af3865c7321d7fed
EOF
```

**Expected Output:**

```
diskpool.openebs.io/pool-on-node-3 created
```

### 7.2 Verify DiskPools Creation

```bash
kubectl get diskpool -n openebs
```

**Sample Output:**

```
NAME             NODE           STATE      POOL_STATUS   CAPACITY   USED   AVAILABLE
pool-on-node-1   ip-10-0-1-61   Creating                 0          0      0
pool-on-node-2   ip-10-0-1-252  Creating                 0          0      0
pool-on-node-3   ip-10-0-1-119  Creating                 0          0      0
```

**Note:** The `STATE` should transition from `Creating` to `Healthy` once the DiskPools are ready.

---

## 8. Label DiskPools

Label each DiskPool to facilitate storage class and PVC scheduling.

### 8.1 Apply Labels to DiskPools

```bash
kubectl label diskpool pool-on-node-1 topology-key=topology-value -n openebs
kubectl label diskpool pool-on-node-2 topology-key=topology-value -n openebs
kubectl label diskpool pool-on-node-3 topology-key=topology-value -n openebs
```

**Expected Output:**

```
diskpool.openebs.io/pool-on-node-1 labeled
diskpool.openebs.io/pool-on-node-2 labeled
diskpool.openebs.io/pool-on-node-3 labeled
```

### 8.2 Verify Labels

```bash
kubectl get diskpool -n openebs -o wide
```

**Sample Output:**

```
NAME             NODE           STATE      POOL_STATUS   CAPACITY   USED   AVAILABLE   TOPOLOGY-KEY
pool-on-node-1   ip-10-0-1-61   Healthy    Ready         20G        0      20G         topology-value
pool-on-node-2   ip-10-0-1-252  Healthy    Ready         20G        0      20G         topology-value
pool-on-node-3   ip-10-0-1-119  Healthy    Ready         20G        0      20G         topology-value
```

**Note:** Ensure that all DiskPools are in the `Healthy` state with available capacity.

---

## 9. Create StorageClass for Mayastor with Replication

Define a `StorageClass` that uses Mayastor for replicated storage.

### 9.1 Create StorageClass Definition

```bash
cat <<EOF | kubectl create -f -
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: mayastor-3
parameters:
  protocol: nvmf
  repl: "3"
provisioner: io.openebs.csi-mayastor
EOF
```

**Expected Output:**

```
storageclass.storage.k8s.io/mayastor-3 created
```

### 9.2 Verify StorageClass

```bash
kubectl get storageclass
```

**Sample Output:**

```
NAME                     PROVISIONER               RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
mayastor-3               io.openebs.csi-mayastor   Delete          Immediate              false                  110s
mayastor-etcd-localpv    openebs.io/local          Delete          WaitForFirstConsumer   false                  36m
mayastor-loki-localpv    openebs.io/local          Delete          WaitForFirstConsumer   false                  36m
openebs-hostpath         openebs.io/local          Delete          WaitForFirstConsumer   false                  36m
openebs-single-replica   io.openebs.csi-mayastor   Delete          Immediate              true                   36m
```

**Note:** `mayastor-3` should be listed with the provisioner `io.openebs.csi-mayastor`.

---

## 10. Create PersistentVolumeClaim (PVC)

Define a PVC that requests storage from the `mayastor-3` StorageClass.

### 10.1 Create PVC Definition

```bash
cat <<EOF | kubectl create -f -
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: ms-volume-claim
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
  storageClassName: mayastor-3
EOF
```

**Expected Output:**

```
persistentvolumeclaim/ms-volume-claim created
```

### 10.2 Verify PVC Status

```bash
kubectl get pvc
```

**Sample Output:**

```
NAME                 STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS       VOLUMEATTRIBUTESCLASS   AGE
local-hostpath-pvc   Bound     pvc-c55026ff-a1c2-43b2-adfa-ba47b60070bd   5G         RWO            openebs-hostpath   <unset>                 32m
ms-volume-claim      Pending
```

**Note:** Initially, `ms-volume-claim` may show as `Pending` while the provisioning is in progress.

### 10.3 Describe PVC (Optional)

To get detailed information about the PVC:

```bash
kubectl describe pvc ms-volume-claim
```

---

## 11. Deploy a Test Pod Using the PVC

Deploy a pod that uses the created PVC to verify storage functionality.

### 11.1 Create Pod Definition

First, create a YAML file named `test-rep-pvc.yml` with the following content:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: fio
spec:
  nodeSelector:
    openebs.io/engine: mayastor
  volumes:
    - name: ms-volume
      persistentVolumeClaim:
        claimName: ms-volume-claim
  containers:
    - name: fio
      image: nixery.dev/shell/fio
      args:
        - sleep
        - "1000000"
      volumeMounts:
        - mountPath: "/volume"
          name: ms-volume
```

**Note:** The `nodeSelector` ensures that the pod is scheduled on a node where Mayastor is running.

### 11.2 Apply the Pod Configuration

```bash
kubectl apply -f test-rep-pvc.yml
```

**Expected Output:**

```
pod/fio created
```

### 11.3 Verify Pod Status

```bash
kubectl get pods
```

**Sample Output:**

```
NAME                       READY   STATUS    RESTARTS   AGE
fio                        1/1     Running   0          27m
hello-local-hostpath-pod   1/1     Running   0          58m
```

**Note:** Ensure that the `fio` pod is in the `Running` state.

---

## 12. Verify the Setup

### 12.1 Check PVC Binding

```bash
kubectl get pvc
```

**Sample Output:**

```
NAME                 STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS       VOLUMEATTRIBUTESCLASS   AGE
local-hostpath-pvc   Bound    pvc-c55026ff-a1c2-43b2-adfa-ba47b60070bd   5G         RWO            openebs-hostpath   <unset>                 62m
ms-volume-claim      Bound    pvc-7e57043d-8f73-436e-b925-6d57c81b29d8   1Gi        RWO            mayastor-3         <unset>                 31m
```

- **Note:** `ms-volume-claim` should transition from `Pending` to `Bound`.

### 12.2 Describe PVC (Optional)

```bash
kubectl describe pvc ms-volume-claim
```

**Sample Output:**

```
Name:          ms-volume-claim
Namespace:     default
StorageClass:  mayastor-3
Status:        Bound
Volume:        pvc-7e57043d-8f73-436e-b925-6d57c81b29d8
...
Events:
  Type     Reason                 Age                From                                                                        Message
  ----     ------                 ----               ----                                                                        -------
  Warning  ProvisioningFailed     15m (x12 over 28m)  io.openebs.csi-mayastor_ip-10-0-1-61_a1313625-9bbb-4170-9f5b-393dba9dd367  failed to provision volume with StorageClass "mayastor-3": rpc error: code = Internal desc = Operation failed: ResourceExhausted("error in response: status code '507 Insufficient Storage', content: 'RestJsonError { details: \"Not enough suitable pools available, 0/1\", message: \"SvcError :: NotEnoughResources: Operation failed due to insufficient resources\", kind: ResourceExhausted }'")
  Normal   ExternalProvisioning   13m (x62 over 28m)  persistentvolume-controller                                                Waiting for a volume to be created either by the external provisioner 'io.openebs.csi-mayastor' or manually by the system administrator. If volume creation is delayed, please verify that the provisioner is running and correctly registered.
  Normal   Provisioning           10m (x13 over 28m)  io.openebs.csi-mayastor_ip-10-0-1-61_a1313625-9bbb-4170-9f5b-393dba9dd367  External provisioner is provisioning volume for claim "default/ms-volume-claim"
  Normal   ProvisioningSucceeded  10m                 io.openebs.csi-mayastor_ip-10-0-1-61_a1313625-9bbb-4170-9f5b-393dba9dd367  Successfully provisioned volume pvc-7e57043d-8f73-436e-b925-6d57c81b29d8
```

**Note:** Initially, you might encounter `ProvisioningFailed` due to insufficient resources. Once sufficient DiskPools are available, the PVC will successfully bind.

### 12.3 Describe Pod

```bash
kubectl describe pod fio
```

**Sample Output:**

```
Name:             fio
Namespace:        default
...
Volumes:
  ms-volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  ms-volume-claim
    ReadOnly:   false
...
Containers:
  fio:
    Container ID:  containerd://1d6f0a0a7e028a3a1e1c0870b34d758580b36fee6f414b5f3f2cb0366af89898
    Image:         nixery.dev/shell/fio
    ...
    Volume Mounts:
      /volume from ms-volume (rw)
...
Events:
  Normal   SuccessfulAttachVolume  11m                attachdetach-controller  AttachVolume.Attach succeeded for volume "pvc-7e57043d-8f73-436e-b925-6d57c81b29d8"
  Normal   Pulling                 11m                kubelet                  Pulling image "nixery.dev/shell/fio"
  Normal   Pulled                  10m                kubelet                  Successfully pulled image "nixery.dev/shell/fio" in 12.584s (12.584s including waiting)
  Normal   Created                 10m                kubelet                  Created container fio
  Normal   Started                 10m                kubelet                  Started container fio
```

### 12.4 Execute FIO Benchmark

Run an I/O benchmark inside the `fio` pod to test the storage performance.

```bash
kubectl exec -it fio -- fio --name=benchtest --size=800m --filename=/volume/test --direct=1 --rw=randrw --ioengine=libaio --bs=4k --iodepth=16 --numjobs=8 --time_based --runtime=60
```

**Sample Output:**

```
fio-3.36
Starting 8 processes
benchtest: Laying out IO file (1 file / 800MiB)
...
RUN status group 0 (all jobs):
   READ: bw=9099KiB/s (9317kB/s), 1121KiB/s-1160KiB/s (1147kB/s-1188kB/s), io=533MiB (559MB), run=60008-60032msec
  WRITE: bw=9110KiB/s (9329kB/s), 1126KiB/s-1162KiB/s (1153kB/s-1190kB/s), io=534MiB (560MB), run=60008-60032msec
...
benchtest: (groupid=0, jobs=1): err= 0: pid=24: Mon Nov 25 18:32:53 2024
  read: IOPS=288, BW=1155KiB/s (1182kB/s)(67.7MiB/60008msec)
  write: IOPS=286, BW=1140KiB/s (1168kB/s)(67.2MiB/60032msec); 0 zone resets
...
```

**Interpretation:**

- **IOPS (Input/Output Operations Per Second):** Indicates the number of read/write operations per second.
- **BW (Bandwidth):** Shows the data transfer rate.
- **Latency:** Measures the time taken to complete operations.

---

## 13. Conclusion

By following this detailed guide, you've successfully:

- **Installed and configured OpenEBS with Mayastor** to provide high-performance, replicated Persistent Volumes on your Kubernetes cluster.
- **Created DiskPools** on each worker node, leveraging AWS EBS volumes for reliable storage.
- **Defined a StorageClass** with replication settings to ensure data redundancy.
- **Deployed a test pod** utilizing the replicated PV to validate the setup.

Your Kubernetes cluster is now equipped with a robust, high-availability storage solution, ready to support your applications with reliable and performant storage.
    
    
---

## References

- [OpenEBS Official Documentation](https://openebs.io/docs/)
- [Mayastor Official Documentation](https://mayastor.io/docs/)
- [Kubernetes Official Documentation](https://kubernetes.io/docs/home/)
- [Helm Official Documentation](https://helm.sh/docs/)
- [Terraform Official Documentation](https://www.terraform.io/docs/index.html)

