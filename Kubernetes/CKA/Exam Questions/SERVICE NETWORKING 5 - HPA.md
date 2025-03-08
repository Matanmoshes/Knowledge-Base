# **Creating a Horizontal Pod Autoscaler with Scale-Down Behavior**

This guide will help you **create a Horizontal Pod Autoscaler (HPA)** that scales based on **CPU utilization** and has **custom scale-down policies** (max 5 pods or 20% of the current replicas, whichever is smaller).

---

## **Question**

```
For this question, set the context to cluster3 by running:

kubectl config use-context cluster3

Create a Horizontal Pod Autoscaler (HPA) named backend-hpa in the cka0841 namespace for a deployment named backend-deployment, which scales based on CPU usage.

The HPA should be configured with:
- A minimum of 3 replicas
- A maximum of 15 replicas

Additionally, set the scale-down behavior to allow:
- Reducing the number of pods by a maximum of 5 at a time
- Or by 20% of the current replica count, whichever results in fewer pods being removed
- This should occur within a time frame of 60 seconds.
```

---

## **Solution & Explanation**

### **Step 1: Set the Kubernetes Context**

Begin by ensuring all commands apply to **cluster3**:

```bash
kubectl config use-context cluster3
```

---

### **Step 2: Understand the HPA Spec**

We want an HPA that:

1. **Targets** the `backend-deployment` in the **cka0841** namespace.
2. **Minimum replicas**: 3
3. **Maximum replicas**: 15
4. **Metrics**: CPU utilization (e.g., 50% average utilization).
5. **Scale-down behavior**:
    - Can reduce up to **5 pods**  
        **OR**
    - **20%** of current replicas
    - Whichever is **lower**, i.e., `selectPolicy: Min`
    - This scale-down limit applies over **60 seconds** (`periodSeconds: 60`).

---

### **Step 3: Create the HPA Manifest**

Create a file, for example `backend-hpa.yaml`:

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: backend-hpa
  namespace: cka0841
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: backend-deployment

  minReplicas: 3
  maxReplicas: 15

  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 50

  behavior:
    scaleDown:
      policies:
      - type: Pods
        value: 5
        periodSeconds: 60
      - type: Percent
        value: 20
        periodSeconds: 60
      selectPolicy: Min
```

#### **Explanation**

- **`apiVersion: autoscaling/v2`** → needed for advanced HPA features (like `behavior`).
- **`scaleTargetRef`** → references the `backend-deployment`.
- **`minReplicas: 3`**, **`maxReplicas: 15`** → sets the scaling range.
- **`metrics.type: Resource`** with **CPU** → scales based on CPU utilization at `50%` average.
- **`behavior.scaleDown.policies`** → defines how the HPA reduces pod replicas:
    - **`type: Pods, value: 5`** → at most 5 pods can be removed in one step within 60s.
    - **`type: Percent, value: 20`** → or 20% of current pods can be removed.
    - **`selectPolicy: Min`** → picks the smaller reduction between 5 pods and 20%.
    - **`periodSeconds: 60`** → all scale-down calculations use a 60-second window.

---

### **Step 4: Apply the HPA**

Use `kubectl apply`:

```bash
kubectl apply -f backend-hpa.yaml
```

Confirm it’s created:

```bash
kubectl get hpa -n cka0841
```

Expected output:

```
NAME          REFERENCE                      TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
backend-hpa   Deployment/backend-deployment  0%/50%    3         15        3          10s
```

---

### **Step 5: Validate HPA Behavior**

1. **Check Current Replicas**:
    
    ```bash
    kubectl get deploy backend-deployment -n cka0841
    ```
    
    Confirm it has at least **3** replicas.
    
2. **Generate Load** or **Simulate Low CPU**:
    
    - If CPU usage is high (over 50%), the HPA will scale up to a max of 15.
    - If CPU usage is below 50% for a while, the HPA will scale down, but only up to **5 pods** or **20%** (whichever is smaller) in any single step.
3. **Check HPA Logs / Events**:
    
    ```bash
    kubectl describe hpa backend-hpa -n cka0841
    ```
    
    Look for scale events (Up or Down) that mention it adjusting replicas.
    

---

## **6. Summary**

✅ **Switched context** to `cluster3`.  
✅ **Created a HorizontalPodAutoscaler** named `backend-hpa` in `cka0841`.  
✅ **Target**: `backend-deployment` with a CPU average utilization metric of 50%.  
✅ **Min / Max Replicas**: `3` to `15`.  
✅ **Scale-down policies**:

- Up to **5** pods removal  
    **OR**
- **20%** of current pods  
    **`selectPolicy: Min`** → use the lower number.
- Within **60 seconds**.

