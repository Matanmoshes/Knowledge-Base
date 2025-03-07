# **Helm Upgrade and Scaling Guide**

This guide will walk you through **updating the Helm repository**, **upgrading the Fluent Bit Helm release (`lvm-crystal-apd`) to version `0.48.5`**, and **scaling the deployment to 3 replicas** on `cluster1-controlplane`.

---

## **Question**

```
SECTION: APPLICATION DEPLOYMENT

For this question, please set the context to cluster1 by running:

kubectl config use-context cluster1

One co-worker deployed a Fluent Bit Helm chart on the cluster1 server called lvm-crystal-apd. A new update has been pushed to the Helm chart, and the team wants you to update the Helm repository to fetch the new changes.

After updating the Helm chart, upgrade its version to 0.48.5 and increase the replica count to 3.

NOTE: - We have to perform this task on the cluster1-controlplane node.

You can SSH into the cluster1 using ssh cluster1-controlplane command.
```

---

## **Solution & Explanation**

### **Step 1: Set the Context to cluster1**

Ensure your commands target **cluster1**:

```bash
kubectl config use-context cluster1
```

This ensures that `kubectl` and `helm` commands interact with the correct Kubernetes cluster.

---

### **Step 2: SSH into cluster1-controlplane**

Since the task must be performed on **cluster1-controlplane**, log in to the node:

```bash
ssh cluster1-controlplane
```

Once inside, proceed with the Helm operations.

---

### **Step 3: Check Installed Helm Releases**

List all Helm releases across all namespaces:

```bash
helm ls -A
```

This will output something like:

```
NAME              	NAMESPACE       REVISION    UPDATED       	STATUS  	CHART            	APP VERSION
lvm-crystal-apd	crystal-apd-ns	1       	2025-03-07 10:00:00 	deployed	fluent-bit-0.47.0	1.9.0
```

- **NAME:** `lvm-crystal-apd` → Name of the deployed Helm release.
- **NAMESPACE:** `crystal-apd-ns` → Namespace where the resources exist.
- **CHART VERSION:** `0.47.0` → Current installed version.
- **APP VERSION:** `1.9.0` → Application version.

---

### **Step 4: Check Available Helm Repositories**

Run:

```bash
helm repo ls
```

Expected output:

```
NAME              	URL
lvm-crystal-apd  	https://charts.lvm-crystal-apd.com/
```

This confirms that `lvm-crystal-apd` is a valid Helm repository.

---

### **Step 5: Update the Helm Repository**

To fetch the latest chart versions, run:

```bash
helm repo update lvm-crystal-apd -n crystal-apd-ns
```

Expected output:

```
Hang tight while we grab the latest from your chart repositories...
Successfully got an update from the "lvm-crystal-apd" chart repository.
```

This updates your local Helm repository cache.

---

### **Step 6: Check Available Versions**

To confirm that **version `0.48.5`** is available:

```bash
helm search repo lvm-crystal-apd/fluent-bit -n crystal-apd-ns -l | head -n30
```

Expected output:

```
NAME                            CHART VERSION	APP VERSION	DESCRIPTION
lvm-crystal-apd/fluent-bit  	0.48.5      	1.9.2        A Fluent Bit Helm chart
lvm-crystal-apd/fluent-bit  	0.47.0      	1.9.0        A Fluent Bit Helm chart
```

Since **0.48.5** is available, we can proceed with the upgrade.

---

### **Step 7: Upgrade the Helm Release**

Upgrade `lvm-crystal-apd` to **version `0.48.5`** and set **replicaCount to 3**:

```bash
helm upgrade lvm-crystal-apd lvm-crystal-apd/fluent-bit -n crystal-apd-ns --version=0.48.5 --set replicaCount=3 --set kind=Deployment
```

### **Explanation**

- **`helm upgrade`** → Upgrades an existing Helm release.
- **`lvm-crystal-apd`** → The Helm release name.
- **`lvm-crystal-apd/fluent-bit`** → The Helm chart name.
- **`--version=0.48.5`** → Specifies that we want to upgrade to version **0.48.5**.
- **`--set replicaCount=3`** → Changes the replica count to **3** dynamically.
- **`--set kind=Deployment`** → Ensures the Helm chart deploys as a `Deployment` resource.

Expected output:

```
Release "lvm-crystal-apd" has been upgraded successfully.
```

---

### **Step 8: Verify the Helm Upgrade**

Run:

```bash
helm ls -n crystal-apd-ns
```

Expected output:

```
NAME              	NAMESPACE       REVISION    UPDATED       	STATUS  	CHART            	APP VERSION
lvm-crystal-apd	crystal-apd-ns	2       	2025-03-07 10:15:00 	deployed	fluent-bit-0.48.5	1.9.2
```

The **CHART** column should now show `fluent-bit-0.48.5`.

---

### **Step 9: Verify the Replica Count**

To check that the deployment now has **3 replicas**, run:

```bash
kubectl get deploy -n crystal-apd-ns
```

Expected output:

```
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
lvm-crystal-apd   3/3     3            3           15m
```

- The **AVAILABLE** column should show `3`, confirming that the deployment scaled successfully.

---

## **10. Summary**

✅ **Set the Kubernetes context** to `cluster1`  
✅ **SSH'd into cluster1-controlplane**  
✅ **Checked Helm releases** (`helm ls -A`)  
✅ **Updated the Helm repository** (`helm repo update`)  
✅ **Verified available versions** (`helm search repo`)  
✅ **Upgraded Fluent Bit to `0.48.5` and set `replicaCount=3`**  
✅ **Verified the Helm upgrade** (`helm ls -n crystal-apd-ns`)  
✅ **Checked that the deployment now has 3 replicas** (`kubectl get deploy`)

