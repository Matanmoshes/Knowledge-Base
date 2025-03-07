# **Creating a Header-Based Routing HTTPRoute in Kubernetes**

This guide will walk you through **creating an `HTTPRoute` named `web-app-route`** in the `ck2145` namespace to **route traffic based on a specific header (`X-Environment: canary`)**.

---

## **Question**

```
Create the web-app-route in the ck2145 namespace. This route should direct requests that contain the header X-Environment: canary to the web-service-canary on port 8080. All other traffic should continue to be routed to web-service also on port 8080.

Note: Gateway has already been created in the nginx-gateway namespace.

To test the gateway, execute the following command:
curl http://cluster3-controlplane:30080

Is the web-app-route created?

Is the traffic, accompanied by the specified header, successfully reaching the web-service-canary?

Is the remaining traffic successfully reaching the web service?
```

---

## **Solution & Explanation**

### **Step 1: Verify the Kubernetes Context**

Ensure you are working in the correct cluster:

```bash
kubectl config use-context cluster3
```

This ensures that all your Kubernetes commands target the **correct cluster**.

---

### **Step 2: Verify Existing Gateway**

Since the **Gateway is already created**, check its configuration:

```bash
kubectl get gateway -n nginx-gateway
```

Expected output:

```
NAME             CLASS   ADDRESS          PORTS
nginx-gateway   nginx   192.168.1.100     80, 443
```

This confirms that the **nginx-gateway** exists and is listening on ports **80 and 443**.

---

### **Step 3: Create the HTTPRoute Manifest**

To configure **header-based traffic routing**, create a file named **`web-app-route.yaml`**:

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: web-app-route
  namespace: ck2145
spec:
  parentRefs:
  - name: nginx-gateway
    namespace: nginx-gateway
  rules:
  - matches:
    - headers:
      - name: X-Environment
        value: canary
    backendRefs:
    - name: web-service-canary
      port: 8080
  - matches:
    - path:
        type: PathPrefix
        value: /
    backendRefs:
    - name: web-service
      port: 8080
```

---

### **Step 4: Explanation of the Manifest**

- **`parentRefs`** → Associates the `HTTPRoute` with the existing **`nginx-gateway`**.
- **`matches[].headers`** → Specifies that requests with the **header `X-Environment: canary`** should be routed to `web-service-canary`.
- **`backendRefs`** → Defines where the traffic is routed:
    - If the header `X-Environment: canary` **is present**, traffic goes to **`web-service-canary:8080`**.
    - If the header **is NOT present**, traffic goes to **`web-service:8080`**.
- **Ordering is Important** → The specific header match rule is **listed first**, followed by the default `/` match.

---

### **Step 5: Apply the HTTPRoute**

Deploy the route using:

```bash
kubectl apply -f web-app-route.yaml
```

Verify that the route has been created:

```bash
kubectl get httproute -n ck2145
```

Expected output:

```
NAME             AGE
web-app-route    5s
```

Check if the HTTPRoute is correctly linked to the **Gateway**:

```bash
kubectl describe httproute web-app-route -n ck2145
```

Look for:

```
Accepted: True
ResolvedRefs: True
```

These confirm that the Gateway controller has successfully processed the route.

---

### **Step 6: Test the Route**

#### **6.1 Verify Canary Traffic (Header-Based Routing)**

Run the following `curl` command to simulate a **request with the `X-Environment: canary` header**:

```bash
curl -H "X-Environment: canary" http://cluster3-controlplane:30080
```

If **successful**, this should return the **response from `web-service-canary`**.

#### **6.2 Verify Default Traffic (Without the Header)**

Now test a request **without** the header:

```bash
curl http://cluster3-controlplane:30080
```

This should return the **response from `web-service`**.

---

### **Step 7: Verify Logs to Confirm Traffic Reaches the Correct Backend**

To **double-check** which service is receiving traffic, inspect the pod logs:

#### **For `web-service-canary`**

```bash
kubectl logs -l app=web-service-canary -n ck2145
```

- You should see logs **only when** the request contains **`X-Environment: canary`**.

#### **For `web-service`**

```bash
kubectl logs -l app=web-service -n ck2145
```

- You should see logs **for all requests** that **do NOT** have the `X-Environment: canary` header.

---

## **8. Summary**

✅ **Set Kubernetes context** to `cluster3`.  
✅ **Verified the existing Gateway** (`nginx-gateway`).  
✅ **Created an HTTPRoute (`web-app-route`)** in `ck2145`.  
✅ **Configured header-based routing** (`X-Environment: canary` → `web-service-canary:8080`).  
✅ **Set a fallback rule** for all other traffic to go to `web-service:8080`.  
✅ **Applied and verified** that the route is active.  
✅ **Tested requests** with and without the `X-Environment: canary` header.

