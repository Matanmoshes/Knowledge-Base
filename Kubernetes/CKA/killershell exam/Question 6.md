Solve this question on: `ssh cka7968`

Create a new _PersistentVolume_ named `safari-pv`. It should have a capacity of _2Gi_, accessMode _ReadWriteOnce_, hostPath `/Volumes/Data` and no storageClassName defined.

Next create a new _PersistentVolumeClaim_ in _Namespace_ `project-t230` named `safari-pvc` . It should request _2Gi_ storage, accessMode _ReadWriteOnce_ and should not define a storageClassName. The _PVC_ should bound to the _PV_ correctly.

Finally create a new _Deployment_ `safari` in _Namespace_ `project-t230` which mounts that volume at `/tmp/safari-data`. The _Pods_ of that _Deployment_ should be of image `httpd:2-alpine`.

---

## Solution

Below is a **step‐by‐step** solution for creating the **PersistentVolume**, **PersistentVolumeClaim**, and **Deployment** as requested. We’ll assume you’re on **`cka7968`** and have `kubectl` access. The final outcome is:

- **PV** named `safari-pv` (2Gi, RWO, hostPath `/Volumes/Data`, no storageClassName).
- **PVC** named `safari-pvc` in **`project-t230`** (2Gi, RWO, no storageClassName).
- **Deployment** named `safari` in **`project-t230`**, with pods of image `httpd:2-alpine` mounting that PVC at `/tmp/safari-data`.

---

## 1. Create the PersistentVolume (PV)

Create a file named `safari-pv.yaml`:

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: safari-pv
spec:
  capacity:
    storage: 2Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /Volumes/Data
  # No storageClassName
  # storageClassName: ""
```

Apply it:

```bash
kubectl apply -f safari-pv.yaml
```

Check:

```bash
kubectl get pv safari-pv
```

You should see `STATUS: Available`.

---

## 2. Create the PersistentVolumeClaim (PVC)

Create a file named `safari-pvc.yaml` in the **`project-t230`** namespace:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: safari-pvc
  namespace: project-t230
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 2Gi
  # No storageClassName
  # storageClassName: ""
```

Apply it:

```bash
kubectl apply -f safari-pvc.yaml
```

Check:

```bash
kubectl get pvc -n project-t230
```

You should see `safari-pvc` with `STATUS: Bound`, referencing `safari-pv` in the `VOLUME` column.

---

## 3. Create the Deployment

Finally, create a file named `safari-deployment.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: safari
  namespace: project-t230
spec:
  replicas: 1
  selector:
    matchLabels:
      app: safari
  template:
    metadata:
      labels:
        app: safari
    spec:
      containers:
      - name: web
        image: httpd:2-alpine
        ports:
        - containerPort: 80
        volumeMounts:
        - name: safari-volume
          mountPath: /tmp/safari-data
      volumes:
      - name: safari-volume
        persistentVolumeClaim:
          claimName: safari-pvc
```

Apply it:

```bash
kubectl apply -f safari-deployment.yaml
```

Verify the pods are running:

```bash
kubectl get pods -n project-t230 -l app=safari
```

Check that the volume is correctly mounted:

```bash
kubectl describe pod -n project-t230 <pod-name>
```

Under `Volumes:`, you should see it referencing `safari-pvc`.

---

## 4. Summary

1. **PV** `safari-pv`: 2Gi, RWO, hostPath `/Volumes/Data`, no storageClass.
2. **PVC** `safari-pvc` (namespace: `project-t230`): requests 2Gi, RWO, no storageClass.
3. **Deployment** `safari` (namespace: `project-t230`): uses `httpd:2-alpine`, mounts the PVC at `/tmp/safari-data`.