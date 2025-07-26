# Question


For this question, please set the context to cluster1 by running:

```
kubectl config use-context cluster1
```

Deploy a Vertical Pod Autoscaler (VPA) named analytics-vpa for a deployment named analytics-deployment in the `cka24456` namespace. The VPA should automatically adjust the CPU and memory requests of the pods to optimize resource utilization. Ensure that the VPA operates in Auto mode, allowing it to evict and recreate pods with updated resource requests as needed.


---

# Step-by-Step Guide

## 1. Switch to the Correct Context

First, ensure all your commands target **cluster1**:

```bash
kubectl config use-context cluster1
```

---

## 2. Verify the Vertical Pod Autoscaler (VPA) Installation

Typically, **VPA** requires its own components (the VPA **Recommender**, **Updater**, and **Admission Controller**) to be installed. Check if you already have the relevant CRDs:

```bash
kubectl get crds | grep verticalpodautoscaler
```

If the CRDs are missing, you’d need to install the VPA components from the [VPA GitHub repo](https://github.com/kubernetes/autoscaler/tree/master/vertical-pod-autoscaler) or another method.  
_(If you’re in a test environment like the CKA, often these components are pre-installed.)_

---

## 3. Create a VPA Manifest

Create a file, for example `analytics-vpa.yaml`:

```yaml
apiVersion: autoscaling.k8s.io/v1
kind: VerticalPodAutoscaler
metadata:
  name: analytics-vpa
  namespace: cka24456
spec:
  targetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: analytics-deployment
  updatePolicy:
    updateMode: "Auto"
```

### Explanation

- **`metadata.name`** = `analytics-vpa` (the name of your VPA).
- **`namespace`** = `cka24456`.
- **`targetRef`** references the **Deployment** you want to scale (`analytics-deployment`).
- **`updatePolicy.updateMode: "Auto"`** allows the VPA to evict and recreate pods automatically with updated resource requests.

---

## 4. Apply the Manifest

Apply your manifest:

```bash
kubectl apply -f analytics-vpa.yaml
```

Check that it was created:

```bash
kubectl get vpa -n cka24456
```

You should see:

```
NAME             AGE
analytics-vpa    30s
```

---

## 5. Verify the VPA is Working

### 5.1 Check VPA Status and Recommendations

Use:

```bash
kubectl describe vpa analytics-vpa -n cka24456
```

Look under **`Status`** → **`Recommendation`**. Once the VPA collects enough usage data, it provides recommended CPU/memory requests for the `analytics-deployment` containers.

### 5.2 Observe Pod Evictions

With **updateMode: "Auto"**, the VPA can evict pods and recreate them with new resource requests. You might see pods in `analytics-deployment` terminating and re-creating as the VPA adjusts them:

```bash
kubectl get pods -n cka24456 -w
```

---

## 6. (Optional) Verify Pod Resource Updates

You can check individual pod specs:

```bash
kubectl get pod <pod-name> -n cka24456 -o yaml
```

Look under **`spec.containers[].resources.requests`** to see the CPU/memory that the VPA sets. Over time, the VPA might raise or lower requests based on usage data.

---

# Summary

1. **Switched context** to cluster1 (`kubectl config use-context cluster1`).
2. **Created** a `VerticalPodAutoscaler` resource named `analytics-vpa` in the `cka24456` namespace.
3. **Set** `updateMode: "Auto"` so the VPA can automatically evict pods and apply new resource requests.
4. **Verified** by describing the VPA and watching for pod restarts with updated requests.

This ensures the `analytics-deployment` in `cka24456` will dynamically optimize its CPU/memory requests based on actual usage. This is especially useful if your workload experiences fluctuations and you want to reduce over‐provisioning or under‐provisioning.