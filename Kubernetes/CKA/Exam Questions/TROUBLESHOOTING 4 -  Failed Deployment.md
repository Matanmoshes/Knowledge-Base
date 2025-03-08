# **Troubleshooting a Failed Deployment in Kubernetes**

Below is a **detailed explanation** of how to troubleshoot and fix a deployment named **`web-dp-cka17-trb`** that has **0/1** pods running in **cluster1**. By following these steps, you’ll identify PVC size mismatches, incorrect init container commands, and invalid liveness probes.

---

## **Question**

```
SECTION: TROUBLESHOOTING

For this question, please set the context to cluster1 by running:

kubectl config use-context cluster1

The deployment called web-dp-cka17-trb has 0 out of 1 pods up and running. Troubleshoot this issue and fix it. Make sure all required POD(s) are in running state and stable (not restarting).

The application runs on port 80 inside the container and is exposed on the node port 30090.
```

---

## **Solution & Explanation**

### **Step 1: Set Context to cluster1**

Ensure all commands apply to **cluster1**:

```bash
kubectl config use-context cluster1
```

---

### **Step 2: Observe the Current Pod Status**

List pods to see if there are any in `Pending`, `CrashLoopBackOff`, or `Error`:

```bash
kubectl get pods
```

If you see **0/1** pods running, the pod might be stuck in `Pending` or repeatedly **crashing**.

---

### **Step 3: Check Events for the Pod**

Identify the name of the failing pod. For example, if it’s `web-dp-cka17-trb-9bdd6779-fm95t`:

```bash
kubectl get events --field-selector involvedObject.name=web-dp-cka17-trb-9bdd6779-fm95t
```

**Common error**:

```
Warning  FailedScheduling pod/web-dp-cka17-trb-9bdd6779-fm95t
0/3 nodes are available: 3 persistentvolumeclaim "web-pvc-cka17-trb" not found...
```

This indicates a **PVC** (PersistentVolumeClaim) issue.

---

### **Step 4: PVC & PV Capacity Mismatch**

1. **Check the PVC**:
    
    ```bash
    kubectl get pvc
    ```
    
    If `web-pvc-cka17-trb` is stuck in `Pending`, inspect it:
    
    ```bash
    kubectl get pvc web-pvc-cka17-trb -o yaml
    ```
    
    Look for `spec.resources.requests.storage:` (e.g. `150Mi`).
    
2. **Check the PV**:
    
    ```bash
    kubectl get pv
    kubectl describe pv web-pv-cka17-trb
    ```
    
    You might find it has a capacity of `100Mi`.
    

**Mismatch**: The PVC wants `150Mi`, but the PV only provides `100Mi`.

#### **Fix the PV Capacity**

Edit the PV to match or exceed the PVC’s request:

```bash
kubectl edit pv web-pv-cka17-trb
```

Change:

```yaml
spec:
  capacity:
    storage: 100Mi
```

to:

```yaml
spec:
  capacity:
    storage: 150Mi
```

Save and exit.

Now, the **PVC** should bind:

```bash
kubectl get pvc web-pvc-cka17-trb
```

You should see `Bound`.

---

### **Step 5: Check Pod Logs & Events Again**

After the PVC binds, the pod might **no longer be Pending** but could still be failing. Check events again:

```bash
kubectl get events --field-selector involvedObject.name=<pod-name>
```

You may see an error such as:

```
Warning   Failed       Error: failed to create containerd task...
"exec: "/bin/bsh\": stat /bin/bsh: no such file or directory"
```

This indicates the **init container command** is referencing **`/bin/bsh`** instead of **`/bin/bash`**.

---

### **Step 6: Fix the Init Container Command**

Edit the deployment and correct the init container’s command:

```bash
kubectl edit deploy web-dp-cka17-trb
```

Look for:

```yaml
initContainers:
- name: some-init
  command: ["/bin/bsh"]
```

Change to:

```yaml
initContainers:
- name: some-init
  command: ["/bin/bash"]
```

Wait for the **pod** to recreate:

```bash
kubectl get pods
```

If it’s still **crashing**, proceed.

---

### **Step 7: Check Liveness Probes & Container Ports**

If the pod is **Init:CrashLoopBackOff** or normal container `CrashLoopBackOff`, check logs and events again:

```
Warning   Unhealthy  pod/web-dp-cka17-trb...   Liveness probe failed:
Get "http://10.50.64.1:81/": dial tcp 10.50.64.1:81: connect: connection refused
```

In the deployment:

```bash
kubectl edit deploy web-dp-cka17-trb
```

You may find a **livenessProbe** like:

```yaml
livenessProbe:
  httpGet:
    path: /
    port: 81
```

But the **containerPort** is `80`. The probe is failing on port `81`.

#### **Fix the Liveness Probe Port**

Change `port: 81` to `port: 80`:

```yaml
livenessProbe:
  httpGet:
    path: /
    port: 80
```

Save and exit.

---

### **Step 8: Final Verification**

1. **Check the Pod** status again:
    
    ```bash
    kubectl get pods
    ```
    
    After a few moments, the pod should become **Running** and stable (no restarts).
    
2. **Confirm NodePort**: The question says the app is exposed on **NodePort 30090**. If a service is set up correctly, you can test from the node:
    
    ```bash
    curl http://<node-ip>:30090
    ```
    
    or if you’re on the **control plane node** (assuming it can route to itself):
    
    ```bash
    curl http://cluster1-controlplane:30090
    ```
    
    You should see the application’s response (e.g. Nginx welcome page or custom app page).
    

---

## **Summary**

1. **Mismatch** in **PV vs. PVC storage** → increased PV from `100Mi` to `150Mi`.
2. **Init container** referencing **`/bin/bsh`** → changed it to **`/bin/bash`**.
3. **Liveness probe** referencing **port 81** while container is on **port 80** → changed probe to **port 80**.

After these fixes, the **deployment** becomes stable and **the pod is running**. You can now **curl** on port **30090** to confirm accessibility.