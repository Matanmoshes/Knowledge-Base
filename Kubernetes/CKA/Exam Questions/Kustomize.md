Below is a **step‐by‐step** way to edit and apply **Kustomize** configurations to remove an unwanted ConfigMap, add a new HPA for both staging and prod, and ensure **prod** has a different max replicas.

---

## 1. Understand the Folder Structure

You have something like:

```
/opt/course/5/api-gateway/
├─ base/
│  ├─ kustomization.yaml
│  ├─ deployment.yaml  (the api-gateway deployment)
│  ├─ ...
├─ staging/
│  └─ kustomization.yaml
├─ prod/
│  └─ kustomization.yaml
```

- The `base/` folder has common resources (like the `api-gateway` Deployment).
- The `staging/` folder references the `base` but might also have environment‐specific patches.
- The `prod/` folder does the same.

Somewhere, you have a **ConfigMap** named `horizontal-scaling-config` that you need to remove, and you need to **add an HPA** resource.

---

## 2. Remove the `horizontal-scaling-config` Completely

1. **Search** for it in the Kustomize configs. Maybe it’s defined in `base/kustomization.yaml` as a `configMapGenerator` or a direct resource reference. Or it could be in `staging/` or `prod/`.
    
2. **Delete** the relevant lines or file references. For example, if you see something like:
    
    ```yaml
    configMapGenerator:
      - name: horizontal-scaling-config
        files:
          - scaling.env
    ```
    
    remove that entire block from the `kustomization.yaml`.
    
3. Also **remove** any references to it under `resources:` or `configMapRef:` in your Deployment or other YAMLs.
    
4. Double‐check that no leftover references remain. (If you do `kustomize build` and see errors referencing that ConfigMap, it means you missed something.)
    

---

## 3. Add an HPA for `api-gateway` With min=2, max=4, CPU=50%

We want a **HorizontalPodAutoscaler** with:

- **name**: `api-gateway`
- **minReplicas**: 2
- **maxReplicas**: 4 (for staging)
- **CPU**: 50% average utilization

Create a new file in the `base` folder named, for example, `hpa.yaml`:

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: api-gateway
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: api-gateway
  minReplicas: 2
  maxReplicas: 4
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 50
```

Then, in `base/kustomization.yaml`, add this file under `resources:`:

```yaml
resources:
  - deployment.yaml
  - hpa.yaml
  # (whatever else)
```

This ensures the HPA is part of the base. Staging will use that default HPA with max=4.

---

## 4. Overwrite the maxReplicas=6 in prod

We only want to change **maxReplicas** in **prod**. We can do this with a Kustomize **patch**. For example, create or edit `prod/hpa-patch.yaml`:

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: api-gateway
spec:
  maxReplicas: 6
```

Then, in `prod/kustomization.yaml`, reference this patch:

```yaml
resources:
  - ../base
patchesStrategicMerge:
  - hpa-patch.yaml
```

So when we kustomize **prod**, the base HPA is patched to `maxReplicas: 6`.

---

## 5. Apply Staging and Prod

Finally, apply each environment:

### **Staging**

```bash
kubectl kustomize /opt/course/5/api-gateway/staging | kubectl apply -f -
```

- Removes the old config map references (if they were included).
- Adds the HPA with `maxReplicas=4`.

### **Prod**

```bash
kubectl kustomize /opt/course/5/api-gateway/prod | kubectl apply -f -
```

- Also removes the old config map references.
- The patch sets `maxReplicas=6`.

---

## 6. Verify Results

1. **No More `horizontal-scaling-config`**
    
    ```bash
    kubectl get configmaps -A | grep horizontal-scaling-config
    ```
    
    You shouldn’t see it anymore.
    
2. **Check HPA in Staging**
    
    ```bash
    kubectl get hpa -n api-gateway-staging
    kubectl describe hpa api-gateway -n api-gateway-staging
    ```
    
    Should show `minReplicas=2, maxReplicas=4, average CPU = 50%`.
    
3. **Check HPA in Prod**
    
    ```bash
    kubectl get hpa -n api-gateway-prod
    kubectl describe hpa api-gateway -n api-gateway-prod
    ```
    
    Should show `minReplicas=2, maxReplicas=6, average CPU = 50%`.
    

At that point, you’ve satisfied all tasks:

- Removed the `horizontal-scaling-config` ConfigMap
- Created a new `api-gateway` HPA with min=2, max=4 (staging) / max=6 (prod), CPU=50% target
- Applied the changes so that they are live in staging and prod.