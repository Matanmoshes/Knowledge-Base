Solve this question on: `ssh cka2556`

Implement the following in _Namespace_ `project-tiger`:

- Create a _Deployment_ named `deploy-important` with `3` replicas
- The _Deployment_ and its _Pods_ should have label `id=very-important`
- First container named `container1` with image `nginx:1-alpine`
- Second container named `container2` with image `google/pause`
- There should only ever be **one** _Pod_ of that _Deployment_ running on **one** worker node, use `topologyKey: kubernetes.io/hostname` for this

> ℹ️ Because there are two worker nodes and the _Deployment_ has three replicas the result should be that the third _Pod_ won't be scheduled. In a way this scenario simulates the behaviour of a _DaemonSet_, but using a _Deployment_ with a fixed number of replicas

---

## Solution

Below is a **step-by-step** solution for creating the **Deployment** named **`deploy-important`** in **`project-tiger`** such that:

1. It has **3 replicas**.
2. Pods (and the Deployment itself) have the label **`id=very-important`**.
3. Each Pod has **two containers**:
    - `container1` using **`nginx:1-alpine`**
    - `container2` using **`google/pause`**
4. Only **one Pod** can be scheduled per node (using **pod anti-affinity** on **topologyKey** `kubernetes.io/hostname`).

Because there are only **2 worker nodes** (assuming the control plane is tainted), the **third** Pod **cannot** be scheduled. This effectively simulates a scenario similar to a DaemonSet but with a fixed number of replicas.

---

## 1. Create the Namespace (If It Doesn’t Already Exist)

```bash
kubectl create namespace project-tiger
```

---

## 2. Create the Deployment Manifest

Create a file named `deploy-important.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deploy-important
  namespace: project-tiger
  labels:
    id: very-important
spec:
  replicas: 3
  selector:
    matchLabels:
      id: very-important
  template:
    metadata:
      labels:
        id: very-important
    spec:
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
                - key: id
                  operator: In
                  values:
                  - very-important
            topologyKey: kubernetes.io/hostname
      containers:
      - name: container1
        image: nginx:1-alpine
      - name: container2
        image: google/pause
```

### Explanation

1. **`replicas: 3`** → We want exactly three replicas in total.
2. **`labels: id=very-important`** → On both the Deployment and Pods.
3. **Two containers**:
    - `container1` → **`nginx:1-alpine`**
    - `container2` → **`google/pause`**
4. **Pod Anti-Affinity**:
    
    ```yaml
    affinity:
      podAntiAffinity:
        requiredDuringSchedulingIgnoredDuringExecution:
        - labelSelector:
            matchExpressions:
              - key: id
                operator: In
                values:
                - very-important
          topologyKey: kubernetes.io/hostname
    ```
    
    This ensures **no two pods** (with label `id=very-important`) can be placed on the **same node**. Because you have 2 worker nodes, the **3rd** Pod can’t be scheduled.

---

## 3. Apply the Deployment

```bash
kubectl apply -f deploy-important.yaml
```

Check:

```bash
kubectl get deployment -n project-tiger
kubectl get pods -n project-tiger -o wide
```

You should see something like:

```
NAME              READY   UP-TO-DATE   AVAILABLE   AGE
deploy-important  2/3     3           2           10s
```

The 3rd Pod should be in a **`Pending`** state because it can’t schedule on the same node as either of the existing 2 pods, and there are only 2 worker nodes.

---

## 4. Summary

- **Deployment**: `deploy-important`, `namespace=project-tiger`, `labels=id=very-important`, `replicas=3`.
- **Two containers**: `container1` (`nginx:1-alpine`), `container2` (`google/pause`).
- **Pod Anti-Affinity** with `topologyKey: kubernetes.io/hostname` ensures only one Pod per worker node.
- Because there are only **2 worker nodes**, the 3rd Pod remains unschedulable as intended.