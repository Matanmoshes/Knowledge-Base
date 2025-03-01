
# Question

For this question, please set the context to cluster3 by running:

```
kubectl config use-context cluster3
```

Extend the web-route on `cka7395` to direct traffic with the path prefix /api to a service named api-service on port 8080, while all other traffic continues to route to web-service.


---

# Guide and Explanation

## 1. Switch Context to Cluster3

First, ensure your local `kubectl` context points to **cluster3**:

```bash
kubectl config use-context cluster3
```

All subsequent `kubectl` commands will now be directed at **cluster3**.

---

## 2. Observe the Current HTTPRoute Configuration

Check the existing `web-route` in the `cka7395` namespace:

```bash
kubectl get httproute web-route -n cka7395 -o yaml
```

You might see something like this:

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: web-route
  namespace: cka7395
spec:
  parentRefs:
  - name: nginx-gateway
    namespace: nginx-gateway
  rules:
  - matches:
    - path:
        type: PathPrefix
        value: /
    backendRefs:
    - name: web-service
      port: 80
```

Currently, **all traffic** (`PathPrefix /`) goes to **`web-service:80`**.

---

## 3. Update the HTTPRoute to Split Traffic

We want:

1. **`/api`** traffic → **`api-service:8080`**
2. **Everything else** → **`web-service:80`**

Create or edit the manifest (e.g., `web-route.yaml`):

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: web-route
  namespace: cka7395
spec:
  parentRefs:
  - name: nginx-gateway
    namespace: nginx-gateway
  rules:
    - matches:
        - path:
            type: PathPrefix
            value: /api
      backendRefs:
        - name: api-service
          port: 8080

    - matches:
        - path:
            type: PathPrefix
            value: /
      backendRefs:
        - name: web-service
          port: 80
```

### Explanation of Key Parts

- **`matches[].path.type: PathPrefix`** indicates that any request whose path _starts_ with that value matches this rule.
- The **first rule** matches `/api`; traffic is routed to **`api-service`** on **port 8080**.
- The **second rule** matches `/`; traffic is routed to **`web-service`** on **port 80**.
- The order matters; most Gateway API implementations match rules in the order listed, so the more specific `/api` is listed first, then the catch‐all `/` rule second.

---

## 4. Apply the Updated Manifest

Apply the changes to your cluster:

```bash
kubectl apply -f web-route.yaml
```

Confirm it’s updated:

```bash
kubectl get httproute web-route -n cka7395 -o yaml
```

You should see both rules (`/api` → `api-service:8080` and `/` → `web-service:80`).

---

## 5. Verify Routing Behavior

### A) Check Status in the Gateway API

```bash
kubectl describe httproute web-route -n cka7395
```

Look for `Accepted` or `Programmed` conditions under the `status` to confirm your Gateway controller recognizes the route.

### B) Check Services and Endpoints

Make sure the **`api-service`** is reachable on **port 8080**:

```bash
kubectl get svc -n cka7395
# Look for api-service with port 8080
```

Check endpoints:

```bash
kubectl describe svc api-service -n cka7395
```

Ensure at least one pod is backing that service. Also verify the `web-service` is still up on port 80.

### C) Test Paths

If your Gateway or load balancer is externally accessible, you can test from outside the cluster (or from a test pod inside the cluster) with:

```bash
curl http://<gateway-address>/
curl http://<gateway-address>/api
```

- `curl /api` should reach **`api-service`**.
- `curl /` (or any path **not** starting with `/api`) should reach **`web-service`**.

Check logs on the respective deployments or pods to confirm traffic arrives at the correct backend.

---

# Recap

1. **Switched context** to `cluster3`.
2. **Inspected** the current `web-route` to see it was routing all traffic to `web-service`.
3. **Modified** the route to add a new rule so that requests with `PathPrefix /api` go to `api-service:8080`, while all other paths (`/`) remain on `web-service:80`.
4. **Verified** by checking `httproute` status, endpoints, and possibly curling the gateway to test different paths.

This approach ensures you have **path‐based routing** in your `HTTPRoute` definition. It’s a common pattern in Kubernetes Gateway API for implementing simple or complex routing logic.