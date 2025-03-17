Solve this question on: `ssh cka5774`

Previously the application `api-gateway` used some external autoscaler which should now be replaced with a _HorizontalPodAutoscaler_ (_HPA_). The application has been deployed to _Namespaces_ `api-gateway-staging` and `api-gateway-prod` like this:

```
kubectl kustomize /opt/course/5/api-gateway/staging | kubectl apply -f -
kubectl kustomize /opt/course/5/api-gateway/prod | kubectl apply -f -
```

Using the Kustomize config at `/opt/course/5/api-gateway` do the following:

1. Remove the _ConfigMap_ `horizontal-scaling-config` completely
2. Add _HPA_ named `api-gateway` for the _Deployment_ `api-gateway` with min `2` and max `4` replicas. It should scale at `50%` average CPU utilisation
3. In prod the _HPA_ should have max `6` replicas
4. Apply your changes for staging and prod so they're reflected in the cluster

---
## Solution

Below is a **step‐by‐step** approach to modify the **Kustomize** configuration in **`/opt/course/5/api-gateway`** so that:

1. The **`horizontal-scaling-config`** ConfigMap is **removed**.
2. A **HorizontalPodAutoscaler** (HPA) named **`api-gateway`** is **added** for the **`api-gateway`** Deployment:
    - Minimum replicas: 2
    - Maximum replicas: 4
    - Target average CPU: 50%
3. In **prod**, the HPA’s **maxReplicas** should be **6** (instead of 4).
4. The changes are then applied to **staging** and **prod**.

You are working on **`cka5774`** with the existing Kustomize structure.

---

## 1. SSH into cka5774

```bash
ssh cka5774
```

---

## 2. Familiarize Yourself with the Kustomize Layout

You should see something like:

```
/opt/course/5/api-gateway/
├─ base/
│  ├─ kustomization.yaml
│  ├─ deployment.yaml   (the api-gateway Deployment)
│  ├─ ...
├─ staging/
│  └─ kustomization.yaml
├─ prod/
│  └─ kustomization.yaml
```

If the **`horizontal-scaling-config`** is in the base or environment overlay, we must remove it from those references.

---

## 3. Remove the `horizontal-scaling-config` ConfigMap

Look in **`base/kustomization.yaml`** (and if needed, `staging/` or `prod/`):

- If it’s declared via `configMapGenerator:`
    
    ```yaml
    configMapGenerator:
    - name: horizontal-scaling-config
      ...
    ```
    
    remove that entire block.
    
- If it’s listed under `resources:` or `patchesStrategicMerge:` referencing that config map, remove those lines.
    

Also check if the `Deployment` references it as an environment variable from a configmap. If so, remove that usage (the question specifically says “remove the ConfigMap horizontal-scaling-config completely”).

---

## 4. Add the HPA to the Base

Create a file in **`base/`** called `hpa.yaml`. Example:

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
  maxReplicas: 4  # default for staging
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 50
```

Then edit `base/kustomization.yaml` to include `hpa.yaml` under `resources:`:

```yaml
resources:
  - deployment.yaml
  - hpa.yaml
  # etc
```

So by default, the HPA is min=2, max=4, CPU=50%.

---

## 5. Patch HPA for Prod to Use max=6

We only want `maxReplicas: 6` in **prod**. Create (or edit) a patch file `prod/hpa-patch.yaml`:

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: api-gateway
spec:
  maxReplicas: 6
```

In **`prod/kustomization.yaml`**, reference this patch:

```yaml
resources:
  - ../base
patchesStrategicMerge:
  - hpa-patch.yaml
```

---

## 6. Apply for Staging and Prod

### 6.1 Staging

```bash
kubectl kustomize /opt/course/5/api-gateway/staging | kubectl apply -f -
```

Removes the old configmap references, sets the default HPA with max=4.

### 6.2 Prod

```bash
kubectl kustomize /opt/course/5/api-gateway/prod | kubectl apply -f -
```

Same changes, **plus** the patch sets `maxReplicas=6` for the HPA.

---

## 7. Verification

1. **No More `horizontal-scaling-config`**
    
    ```bash
    kubectl get configmaps -A | grep horizontal-scaling-config
    ```
    
    Should yield **no** results.
    
2. **Check HPA in Staging**
    
    ```bash
    kubectl get hpa -n api-gateway-staging
    ```
    
    Should see `api-gateway` with `min=2, max=4, CPU=50%`.
    
3. **Check HPA in Prod**
    
    ```bash
    kubectl get hpa -n api-gateway-prod
    ```
    
    The same `api-gateway` HPA, but `maxReplicas=6`.
    

Thus, you have:

- Removed `horizontal-scaling-config`.
- Created an `api-gateway` HPA with min=2, max=4 (staging), and max=6 (prod), CPU=50% target usage.
- Applied the changes so the cluster is updated.