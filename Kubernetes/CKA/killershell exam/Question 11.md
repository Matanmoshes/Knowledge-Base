Solve this question on: `ssh cka2556`

Use _Namespace_ `project-tiger` for the following. Create a _DaemonSet_ named `ds-important` with image `httpd:2-alpine` and labels `id=ds-important` and `uuid=18426a0b-5f59-4e10-923f-c0e078e82462`. The _Pods_ it creates should request 10 millicore cpu and 10 mebibyte memory. The _Pods_ of that _DaemonSet_ should run on all nodes, also controlplanes.

---

## Solution

Below is a **step‐by‐step** solution to create the **DaemonSet** named **`ds-important`** with the specified resource requests, labels, and tolerations so that it runs on **all nodes** including control planes. We’ll assume you’re on **`cka2556`**, using the **`project-tiger`** namespace.

---

## 1. Create the Namespace (If Needed)

```bash
kubectl create namespace project-tiger
```

(If it already exists, this command is harmless.)

---

## 2. Create the DaemonSet Manifest

Create a file, for example `ds-important.yaml`:

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: ds-important
  namespace: project-tiger
  labels:
    id: ds-important
    uuid: 18426a0b-5f59-4e10-923f-c0e078e82462
spec:
  selector:
    matchLabels:
      id: ds-important
  updateStrategy:
    type: RollingUpdate
  template:
    metadata:
      labels:
        id: ds-important
        uuid: 18426a0b-5f59-4e10-923f-c0e078e82462
    spec:
      # Tolerations so pods run on control planes too
      tolerations:
      - key: node-role.kubernetes.io/master
        effect: NoSchedule
        operator: Exists
      - key: node-role.kubernetes.io/control-plane
        effect: NoSchedule
        operator: Exists

      containers:
      - name: main
        image: httpd:2-alpine
        resources:
          requests:
            cpu: "10m"
            memory: "10Mi"
```

### Key Points

- **`tolerations`** ensure the DaemonSet schedules on control plane nodes that have taints like `node-role.kubernetes.io/master` or `node-role.kubernetes.io/control-plane`.
- **`resources.requests`** set CPU to **10m** and Memory to **10Mi**.
- **`labels`** for the DaemonSet and Pod:
    - `id: ds-important`
    - `uuid: 18426a0b-5f59-4e10-923f-c0e078e82462`

---

## 3. Apply the DaemonSet

```bash
kubectl apply -f ds-important.yaml
```

Verify it:

```bash
kubectl get daemonsets -n project-tiger
```

You should see something like:

```
NAME          DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
ds-important  3         3         3       3            3          <none>          10s
```

_(The exact numbers depend on how many nodes are in the cluster.)_

---

## 4. Confirm Pods Are on Control Planes Too

Check the pods:

```bash
kubectl get pods -n project-tiger -o wide
```

Ensure there’s a pod scheduled on each worker node **and** the control plane node(s). If you see pods on each node, you have met the requirement that the DaemonSet runs everywhere.

---

## 5. Summary

You’ve created a DaemonSet:

- **Name**: `ds-important`
- **Namespace**: `project-tiger`
- **Image**: `httpd:2-alpine`
- **Requests**: 10m CPU / 10Mi memory
- **Labels**: `id=ds-important`, `uuid=18426a0b-5f59-4e10-923f-c0e078e82462`
- **Tolerations**: Allows scheduling on control planes

