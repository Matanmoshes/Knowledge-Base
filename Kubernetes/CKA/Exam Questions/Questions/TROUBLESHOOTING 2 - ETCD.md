# Question

```
For this question, please set the context to cluster4 by running:

kubectl config use-context cluster4

The controlplane node called cluster4-controlplane in the cluster4 cluster is planned for a regular maintenance. In preparation for this maintenance work, we need to take backups of this cluster. However, something is broken at the moment!

Troubleshoot the issues and take a snapshot of the ETCD database using the etcdctl utility at the location /opt/etcd-boot-cka18-trb.db.

Note: Make sure etcd listens at its default port. Also you can SSH to the cluster4-controlplane host using the ssh cluster4-controlplane command from the student-node.
```

---

# Step-by-Step Guide

## 1. Set the Context to cluster4

On your local (student-node) terminal:

```bash
kubectl config use-context cluster4
```

All subsequent `kubectl` commands will target **cluster4**.

---

## 2. SSH into cluster4-controlplane

Since you need direct access to etcd (and the `/etc/kubernetes/manifests` directory), SSH into the control plane node:

```bash
ssh cluster4-controlplane
```

---

## 3. Attempt the ETCD Backup

Try the standard etcdctl backup command:

```bash
ETCDCTL_API=3 etcdctl \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key \
  snapshot save /opt/etcd-boot-cka18-trb.db
```

You may find it **hangs** or fails due to the underlying **etcd** or **kube-apiserver** being unhealthy.

---

## 4. Investigate the Cluster State

### 4.1 Check Pods (kube-system)

```bash
kubectl get pods -A
```

If it returns:

```
The connection to the server cluster4-controlplane:6443 was refused
```

it suggests the **apiserver** is not functioning properly.

### 4.2 Look at Kubelet Logs

Since the API server is down, investigate the **kubelet** logs:

```bash
journalctl -u kubelet -f
```

You might see **connection refused** spam as the kubelet fails to connect to the local API server. Filter out those lines:

```bash
journalctl -u kubelet -f | grep -v 'connect: connection refused'
```

Look for errors that might reveal the root cause.

---

## 5. Find the Etcd Manifest Issue

In the logs, you might see:

```
E0923 04:38:15.630925 ... "Could not process manifest file" 
err="invalid pod: [spec.containers[0].volumeMounts[1].name: Not found: \"etcd-cert\"]" 
path="/etc/kubernetes/manifests/etcd.yaml"
```

So the **etcd static pod** is failing to start because it references a **volumeMount** named `etcd-cert` while the **volume** is actually named `etcd-certs` (a mismatch).

### 5.1 Fix the etcd.yaml

Open the etcd manifest:

```bash
sudo vi /etc/kubernetes/manifests/etcd.yaml
```

Look for a snippet like:

```yaml
volumeMounts:
- name: etcd-cert  # <-- This might be a typo
  mountPath: /etc/kubernetes/pki/etcd

volumes:
- name: etcd-certs # <-- Actual volume name
  hostPath:
    path: /etc/kubernetes/pki/etcd
```

Correct the **volumeMount** name to match the volume name (`etcd-certs`, for example). Save and exit.

---

## 6. Restart Kubelet and Wait

Restart kubelet so it re‐processes the changed manifest:

```bash
sudo systemctl restart kubelet
```

Wait a minute or two for the **etcd** and **kube-apiserver** static pods to come back up. Check if the cluster recovers:

```bash
kubectl get pods -A
```

You should now see pods in **Running** status.

---

## 7. Retake the Etcd Backup

With etcd healthy again, run the backup command:

```bash
ETCDCTL_API=3 etcdctl \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key \
  snapshot save /opt/etcd-boot-cka18-trb.db
```

Now it should **succeed** quickly without hanging.

Verify the backup file:

```bash
ls -l /opt/etcd-boot-cka18-trb.db
```

And optionally confirm it’s valid:

```bash
ETCDCTL_API=3 etcdctl snapshot status /opt/etcd-boot-cka18-trb.db
```

---

# Summary

1. **Symptom**: `kubectl` commands fail with “connection refused,” the **etcd** or **apiserver** pods repeatedly fail to start.
2. **Logs**: Observed an error indicating a mismatch in volumeMount (`etcd-cert`) vs. volume name (`etcd-certs`) in `/etc/kubernetes/manifests/etcd.yaml`.
3. **Fix**: Edited the manifest to correct the volumeMount name → restarted kubelet → etcd and apiserver came up healthy.
4. **Result**: Successfully took an **ETCD snapshot** using `etcdctl` at `/opt/etcd-boot-cka18-trb.db`.

---

## Key Takeaways for CKA

- **Static Pods** for control plane components (etcd, kube-apiserver, controller-manager, scheduler) live under `/etc/kubernetes/manifests/`.
- If **kube-apiserver** or **etcd** are down, `kubectl` commands usually fail. Always **check** the kubelet logs or static pod logs.
- A **volumeMount** name mismatch can break the entire component. Fixing it in the manifest is crucial.
- **Restart** kubelet so it re‐reads the manifest.
- Once etcd is healthy, you can run your `etcdctl` snapshot commands to back up the cluster state.