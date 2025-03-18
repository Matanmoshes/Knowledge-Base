Solve this question on: `ssh cka7968`

There was a security incident where an intruder was able to access the whole cluster from a single hacked backend _Pod_.

To prevent this create a _NetworkPolicy_ called `np-backend` in _Namespace_ `project-snake`. It should allow the `backend-*` _Pods_ only to:

- Connect to `db1-*` _Pods_ on port `1111`
- Connect to `db2-*` _Pods_ on port `2222`

Use the `app` _Pod_ labels in your policy.

> ℹ️ All _Pods_ in the _Namespace_ run plain Nginx images. This allows simple connectivity tests like: `k -n project-snake exec POD_NAME -- curl POD_IP:PORT`

> ℹ️ For example, connections from `backend-*` _Pods_ to `vault-*` _Pods_ on port `3333` should no longer work

---

## Solution

Below is a **step‐by‐step** solution to create a **NetworkPolicy** named **`np-backend`** in the **`project-snake`** namespace. It ensures that **backend Pods** can only connect **(egress)** to **db1 Pods on port 1111** and **db2 Pods on port 2222**.

---

## 1. Create the NetworkPolicy Manifest

Create a file, for example `np-backend.yaml`, with the following content:

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: np-backend
  namespace: project-snake
spec:
  podSelector:
    matchLabels:
      app: backend
  policyTypes:
  - Egress
  egress:
    - to:
      - podSelector:
          matchLabels:
            app: db1
      ports:
      - protocol: TCP
        port: 1111
    - to:
      - podSelector:
          matchLabels:
            app: db2
      ports:
      - protocol: TCP
        port: 2222
```

### Explanation

1. **`metadata.name: np-backend`** → The NetworkPolicy name is **`np-backend`**.
2. **`namespace: project-snake`** → Lives in the **`project-snake`** namespace.
3. **`podSelector.matchLabels.app: backend`** → This policy **applies** to Pods labeled **`app=backend`**.
4. **`policyTypes: [ Egress ]`** → We’re controlling **outgoing** connections.
5. **`egress`** rules allow:
    - Egress to pods labeled **`app=db1`** on **port 1111**.
    - Egress to pods labeled **`app=db2`** on **port 2222**.
6. **All other egress** is implicitly **denied**, which blocks `backend-*` pods from connecting to, e.g., **`vault-*`** on port 3333 or anything else.

---

## 2. Apply the NetworkPolicy

On **`cka7968`**:

```bash
kubectl apply -f np-backend.yaml
```

Check it:

```bash
kubectl get networkpolicies -n project-snake
```

You should see:

```
NAME         POD-SELECTOR       AGE
np-backend   app=backend        5s
```

---

## 3. Verify Connections

If you want to test:

1. **Create or identify** some pods labeled `app=backend`, `app=db1`, `app=db2`, etc.
2. **Attempt** from a `backend` pod to `curl` a `db1` pod on port `1111` → should **succeed**.
3. **Attempt** to `curl` a `db2` pod on port `2222` → should **succeed**.
4. **Attempt** to `curl` a `vault` pod or any other label/port → should **fail**.