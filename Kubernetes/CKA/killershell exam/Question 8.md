Solve this question on: `ssh cka3962`

Your coworker notified you that node `cka3962-node1` is running an older Kubernetes version and is not even part of the cluster yet.

1. Update the node's Kubernetes to the exact version of the controlplane
    
2. Add the node to the cluster using kubeadm
    

> ℹ️ You can connect to the worker node using `ssh cka3962-node1` from `cka3962`

---

## Solution

Below is a **step‐by‐step** approach to updating **`cka3962-node1`** to match the **control plane** version and rejoin it to the cluster. Since you’re in an exam environment, the exact package commands depend on your OS (Debian/Ubuntu vs. CentOS/RHEL). We’ll show a general approach that can be adapted.

---

## 1. SSH into the Control Plane (cka3962)

You’re already on **`cka3962`** (the control plane). First, **check** the Kubernetes version on the control plane:

```bash
kubectl version --short
```

Example output:

```
Client Version: v1.24.7
Server Version: v1.24.7
```

So the cluster’s **control plane** is at version **`1.24.7`**.

_(Alternatively, you can see `kubeadm version`, or if you have packages, `dpkg -l | grep kubeadm` or `rpm -qa | grep kubeadm` to confirm the exact versions.)_

---

## 2. SSH into the Worker Node (cka3962-node1)

On the control plane, run:

```bash
ssh cka3962-node1
```

Now you’re on the node that’s not part of the cluster yet and has an older version of Kubernetes.

---

## 3. Update Kubernetes Packages on cka3962-node1

### 3.1 Remove (or update) Old Packages

If you have older packages:

```bash
sudo apt-get remove -y kubelet kubeadm kubectl
```

_(For Debian/Ubuntu)_ or the equivalent **yum/dnf remove** on RHEL/CentOS.

### 3.2 Install the Matching Version

If you’re on Debian/Ubuntu:

```bash
sudo apt-get update
sudo apt-get install -y \
  kubelet=1.24.7-00 \
  kubeadm=1.24.7-00 \
  kubectl=1.24.7-00
sudo apt-mark hold kubelet kubeadm kubectl
```

_(Adjust the version to exactly match the control plane’s major.minor.patch, e.g. `1.24.7-00`. If your environment uses a different version or distribution, adapt accordingly.)_

Then,

```bash
sudo systemctl daemon-reload
sudo systemctl enable kubelet
sudo systemctl restart kubelet
```

---

## 4. Generate the Join Command from the Control Plane

Return to the control plane node **(cka3962)** or open another session. Use **kubeadm** to get the token and CA hash:

```bash
kubeadm token create --print-join-command
```

Example output:

```
kubeadm join 10.11.12.13:6443 --token abc123.def456 \
   --discovery-token-ca-cert-hash sha256:abcdef123...
```

> **Tip**: If the token is already created, you can also do `kubeadm token list`. If the token is expired, re‐create one with the above command.

---

## 5. Run Join Command on cka3962-node1

Back on **`cka3962-node1`**:

```bash
sudo kubeadm join 10.11.12.13:6443 --token abc123.def456 \
  --discovery-token-ca-cert-hash sha256:abcdef123...
```

_(Use the exact command + IP/token from your environment.)_

You should see logs indicating the node has successfully joined.

---

## 6. Verify Node is Part of the Cluster

Finally, on the control plane (cka3962), check:

```bash
kubectl get nodes
```

You should see **`cka3962-node1`** in the `Ready` state (after a brief moment).

---

## Summary

1. **Identify** the control plane version (e.g., `1.24.7`).
2. **SSH** to `cka3962-node1` and **install** matching versions of `kubeadm`, `kubectl`, `kubelet`.
3. **Generate** the join command on the control plane using `kubeadm token create --print-join-command`.
4. **Run** the join command on `cka3962-node1`.
5. **Confirm** the node is in the cluster via `kubectl get nodes`.

