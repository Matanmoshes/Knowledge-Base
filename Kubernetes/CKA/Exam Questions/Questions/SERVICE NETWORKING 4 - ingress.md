# **Creating an Ingress Resource for Nginx in Kubernetes**

This guide will help you create an **Ingress resource** named **`nginx-ingress-cka04-svcn`** to expose **`nginx-deployment-cka04-svcn`** via **`nginx-service-cka04-svcn`**, with **SSL redirect disabled**.

---

## **Question**

```
There is a deployment nginx-deployment-cka04-svcn in cluster3 which is exposed using service nginx-service-cka04-svcn.

Create an ingress resource nginx-ingress-cka04-svcn to load balance the incoming traffic with the following specifications:

- pathType: Prefix and path: /
- Backend Service Name: nginx-service-cka04-svcn
- Backend Service Port: 80
- ssl-redirect is set to false
```

---

## **Solution & Explanation**

### **Step 1: Set the Context to Cluster3**

Ensure you are working in **cluster3**:

```bash
kubectl config use-context cluster3
```

This ensures that `kubectl` commands are executed in the correct cluster.

---

### **Step 2: Verify Existing Deployment and Service**

Before creating an **Ingress**, confirm that the **Deployment** and **Service** exist.

```bash
kubectl get deployments.apps nginx-deployment-cka04-svcn
kubectl get svc nginx-service-cka04-svcn
```

Expected output:

```
NAME                              READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment-cka04-svcn       3/3     3            3           10m

NAME                              TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
nginx-service-cka04-svcn          ClusterIP   10.96.124.34    <none>        80/TCP    10m
```

This confirms that: ✅ The **Nginx deployment** is running.  
✅ The **service** `nginx-service-cka04-svcn` exists and exposes port `80`.

---

### **Step 3: Create the Ingress Resource**

Create a file called **`nginx-ingress-cka04-svcn.yaml`** with the following content:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: nginx-ingress-cka04-svcn
  namespace: default
  annotations:
    nginx.ingress.kubernetes.io/ssl-redirect: "false"  # Disable SSL redirect
spec:
  ingressClassName: nginx
  rules:
  - host: nginx.example.com  # Change this to your domain or remove for default routing
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: nginx-service-cka04-svcn
            port:
              number: 80
```

---

### **Step 4: Explanation of the Ingress Configuration**

- **`metadata.name: nginx-ingress-cka04-svcn`** → The name of the Ingress.
- **`nginx.ingress.kubernetes.io/ssl-redirect: "false"`** → Ensures HTTPS requests are **not** automatically redirected to HTTPS.
- **`spec.ingressClassName: nginx`** → Specifies that the Ingress is managed by the Nginx Ingress Controller.
- **`host: nginx.example.com`** → (Optional) Set this to your domain. If left out, the Ingress will match requests based on the default settings.
- **`pathType: Prefix`** → Ensures all requests matching `/` are routed to the **Nginx service**.
- **`backend.service.name: nginx-service-cka04-svcn`** → Points to the **service that exposes the Nginx deployment**.
- **`backend.service.port.number: 80`** → Routes traffic to **port 80**.

---

### **Step 5: Apply the Ingress Resource**

Run:

```bash
kubectl apply -f nginx-ingress-cka04-svcn.yaml
```

Verify that the Ingress is created:

```bash
kubectl get ingress nginx-ingress-cka04-svcn
```

Expected output:

```
NAME                          CLASS   HOSTS                ADDRESS        PORTS   AGE
nginx-ingress-cka04-svcn      nginx   nginx.example.com   <pending>      80      5s
```

If **ADDRESS** is `<pending>`, wait for the Ingress Controller to provision the external IP.

---

### **Step 6: Verify Ingress Routing**

Once the Ingress is active, you can test access:

#### **6.1 Test Access Using the Ingress Hostname**

```bash
curl -H "Host: nginx.example.com" http://<INGRESS-IP>/
```

#### **6.2 Find the Ingress IP**

If you don’t have a domain set up, retrieve the Ingress IP:

```bash
kubectl get ingress nginx-ingress-cka04-svcn -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

Then run:

```bash
curl http://<Ingress-IP>/
```

If everything is set up correctly, you should see the **Nginx default page**:

```
<html>
<head><title>Welcome to nginx!</title></head>
...
</html>
```

---

### **Step 7: Debugging Ingress Issues (If Needed)**

#### **7.1 Check Ingress Events**

If the Ingress is not working, inspect its events:

```bash
kubectl describe ingress nginx-ingress-cka04-svcn
```

Look for **error messages** like:

```
Warning  BackendNotFound  No endpoints found for service "nginx-service-cka04-svcn"
```

- This means the service might not be **targeting the correct deployment labels**.

#### **7.2 Check Logs of the Ingress Controller**

```bash
kubectl logs -l app.kubernetes.io/name=ingress-nginx -n ingress-nginx
```

If the logs show errors, the **Ingress Controller may not be correctly installed**.

---

## **8. Summary**

✅ **Set the Kubernetes context** to `cluster3`.  
✅ **Verified the deployment and service** (`nginx-deployment-cka04-svcn` and `nginx-service-cka04-svcn`).  
✅ **Created an Ingress resource** (`nginx-ingress-cka04-svcn`).  
✅ **Set path-based routing** (`/` → `nginx-service-cka04-svcn:80`).  
✅ **Disabled SSL redirect** to prevent forced HTTPS.  
✅ **Applied and verified the Ingress** (`kubectl get ingress`).  
✅ **Tested the routing with `curl`**.

