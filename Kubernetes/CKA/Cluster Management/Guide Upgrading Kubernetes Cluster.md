# Guide to Upgrading a Kubernetes Cluster

Upgrading a Kubernetes cluster is a critical task that ensures you have the latest features, security patches, and performance improvements. This guide provides a step-by-step approach to upgrading your Kubernetes cluster safely and efficiently.

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Upgrade the Control Plane](#upgrade-the-control-plane)
    - [Upgrade `kubeadm`](#upgrade-kubeadm-on-control-plane)
    - [Drain the Control Plane Node](#drain-the-control-plane-node)
    - [Plan the Upgrade](#plan-the-upgrade)
    - [Apply the Upgrade](#apply-the-upgrade)
    - [Upgrade `kubelet` and `kubectl`](#upgrade-kubelet-and-kubectl-on-control-plane)
    - [Restart `kubelet`](#restart-kubelet-on-control-plane)
    - [Uncordon the Control Plane Node](#uncordon-the-control-plane-node)
3. [Upgrade the Worker Nodes](#upgrade-the-worker-nodes)
    - [Worker Node 1](#worker-node-1)
    - [Worker Node 2](#worker-node-2)
4. [Verify the Cluster Upgrade](#verify-the-cluster-upgrade)
5. [Conclusion](#conclusion)

---

## Prerequisites

- **Backup your cluster data**: Always ensure you have a backup before starting the upgrade process.
- **Administrative access**: You should have `sudo` privileges on all nodes.
- **Stable cluster**: Ensure the cluster is in a healthy state (`kubectl get nodes` shows all nodes as `Ready`).
- **Network connectivity**: All nodes can communicate with each other.

**Note**: Replace placeholder values like `<CONTROL_PLANE_NODE_NAME>` and `<WORKER_NODE_PUBLIC_IP>` with your actual node names and IP addresses.

---

## Upgrade the Control Plane

### Upgrade `kubeadm` on Control Plane

First, update the `kubeadm` package on the control plane node to the desired version (e.g., `1.27.2-1`):

```bash
sudo apt-get update
sudo apt-get install -y --allow-change-held-packages kubeadm=1.27.2-1
```

Verify the upgrade:

```bash
kubeadm version
```

### Drain the Control Plane Node

Prepare the control plane node for maintenance by draining it:

```bash
kubectl drain <CONTROL_PLANE_NODE_NAME> --ignore-daemonsets --delete-local-data
```

### Plan the Upgrade

Check what the upgrade would entail:

```bash
sudo kubeadm upgrade plan v1.27.2
```

### Apply the Upgrade

Proceed to upgrade the control plane components:

```bash
sudo kubeadm upgrade apply v1.27.2
```

### Upgrade `kubelet` and `kubectl` on Control Plane

Update the `kubelet` and `kubectl` packages:

```bash
sudo apt-get update
sudo apt-get install -y --allow-change-held-packages kubelet=1.27.2-1 kubectl=1.27.2-1
```

### Restart `kubelet` on Control Plane

Reload systemd configurations and restart `kubelet`:

```bash
sudo systemctl daemon-reload
sudo systemctl restart kubelet
```

### Uncordon the Control Plane Node

Bring the control plane node back into service:

```bash
kubectl uncordon <CONTROL_PLANE_NODE_NAME>
```

---

## Upgrade the Worker Nodes

**Important**: Upgrade worker nodes one at a time to maintain cluster availability.

### Worker Node 1

**Step 1**: Drain Worker Node 1 from the control plane:

```bash
kubectl drain <WORKER_NODE_1_NAME> --ignore-daemonsets --delete-local-data
```

**Step 2**: SSH into Worker Node 1:

```bash
ssh user@<WORKER_NODE_1_PUBLIC_IP>
```

**Step 3**: Upgrade `kubeadm`:

```bash
sudo apt-get update
sudo apt-get install -y --allow-change-held-packages kubeadm=1.27.2-1
```

**Step 4**: Verify `kubeadm` version:

```bash
kubeadm version
```

**Step 5**: Upgrade the node configuration:

```bash
sudo kubeadm upgrade node
```

**Step 6**: Upgrade `kubelet` and `kubectl`:

```bash
sudo apt-get update
sudo apt-get install -y --allow-change-held-packages kubelet=1.27.2-1 kubectl=1.27.2-1
```

**Step 7**: Restart `kubelet`:

```bash
sudo systemctl daemon-reload
sudo systemctl restart kubelet
```

**Step 8**: Exit the node and uncordon it from the control plane:

```bash
exit
kubectl uncordon <WORKER_NODE_1_NAME>
```

### Worker Node 2

Repeat the same steps for Worker Node 2.

**Step 1**: Drain Worker Node 2:

```bash
kubectl drain <WORKER_NODE_2_NAME> --ignore-daemonsets --delete-local-data
```

**Step 2**: SSH into Worker Node 2:

```bash
ssh user@<WORKER_NODE_2_PUBLIC_IP>
```

**Step 3**: Upgrade `kubeadm`:

```bash
sudo apt-get update
sudo apt-get install -y --allow-change-held-packages kubeadm=1.27.2-1
```

**Step 4**: Verify `kubeadm` version:

```bash
kubeadm version
```

**Step 5**: Upgrade the node configuration:

```bash
sudo kubeadm upgrade node
```

**Step 6**: Upgrade `kubelet` and `kubectl`:

```bash
sudo apt-get update
sudo apt-get install -y --allow-change-held-packages kubelet=1.27.2-1 kubectl=1.27.2-1
```

**Step 7**: Restart `kubelet`:

```bash
sudo systemctl daemon-reload
sudo systemctl restart kubelet
```

**Step 8**: Exit the node and uncordon it:

```bash
exit
kubectl uncordon <WORKER_NODE_2_NAME>
```

---

## Verify the Cluster Upgrade

On the control plane node, check the status of all nodes:

```bash
kubectl get nodes
```

You should see all nodes in the `Ready` state with the updated version:

```
NAME               STATUS   ROLES           AGE    VERSION
control-plane      Ready    control-plane   100d   v1.27.2
worker-node-1      Ready    <none>          100d   v1.27.2
worker-node-2      Ready    <none>          100d   v1.27.2
```

If any nodes show `NotReady`, wait a few moments and check again.


---

**Disclaimer**: Always refer to the [official Kubernetes documentation](https://kubernetes.io/docs/tasks/administer-cluster/cluster-upgrade/) for the most accurate and up-to-date instructions.