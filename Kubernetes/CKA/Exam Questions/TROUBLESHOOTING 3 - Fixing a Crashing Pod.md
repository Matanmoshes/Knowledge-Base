# **Troubleshooting and Fixing a Crashing Pod in Kubernetes**

This guide will help you **identify and fix** a continuously crashing pod named **`nginx-cka01-trb`** in **cluster1**. By the end of this guide, you should be able to **start the pod successfully** and **access the web service**.

---

## **Question**

```
For this question, please set the context to cluster1 by running:

kubectl config use-context cluster1

A pod called nginx-cka01-trb is running in the default namespace. There is a container called nginx-container running inside this pod that uses the image nginx:latest. There is another sidecar container called logs-container that runs in this pod.

For some reason, this pod is continuously crashing. Identify the issue and fix it. Make sure that the pod is in a running state and you are able to access the website using the command:
curl http://cluster1-controlplane:30001

This must be done on the control plane node of cluster1.
```

---

## **Solution & Explanation**

### **Step 1: Set the Kubernetes Context**

Ensure all your commands target **cluster1**:

```bash
kubectl config use-context cluster1
```

---

### **Step 2: Check Pod Status**

List the pods to check their current state:

```bash
kubectl get pods
```

If the pod is in **`CrashLoopBackOff`** or **`Error`**, proceed with debugging.

---

### **Step 3: Check Logs of nginx-container**

Since the pod has **two containers**, check logs for the primary **nginx-container**:

```bash
kubectl logs -f nginx-cka01-trb -c nginx-container
```

### **Issue 1: Image Pull Failure**

If you see an error such as:

```
Error response from daemon: manifest for nginx:latst not found
```

This means the image name is incorrect (`nginx:latst` instead of `nginx:latest`).

#### **Fix the Image Name**

Edit the pod:

```bash
kubectl edit pod nginx-cka01-trb
```

Look for this section:

```yaml
containers:
  - name: nginx-container
    image: nginx:latst  # Incorrect
```

Fix it to:

```yaml
containers:
  - name: nginx-container
    image: nginx:latest  # Corrected
```

Save and exit.

Verify if the pod is running:

```bash
kubectl get pods
```

---

### **Step 4: Check Logs for the Sidecar logs-container**

If the pod is still crashing, check logs for the **logs-container**:

```bash
kubectl logs -f nginx-cka01-trb -c logs-container
```

If you see:

```
cat: can't open '/var/log/httpd/access.log': No such file or directory
cat: can't open '/var/log/httpd/error.log': No such file or directory
```

The issue is that **logs-container** is looking for logs in **`/var/log/httpd/`**, but the actual mounted volume is **`/var/log/nginx/`**.

---

### **Step 5: Fix the Log File Path**

1. Save the current pod definition:
    
    ```bash
    kubectl get pod nginx-cka01-trb -o yaml > /tmp/test.yaml
    ```
    
2. Edit the file:
    
    ```bash
    vi /tmp/test.yaml
    ```
    
3. Find the `command` section under `logs-container`:
    
    ```yaml
    command: ["cat", "/var/log/httpd/access.log", "/var/log/httpd/error.log"]
    ```
    
    Change it to:
    
    ```yaml
    command: ["cat", "/var/log/nginx/access.log", "/var/log/nginx/error.log"]
    ```
    
4. **Delete the crashing pod**:
    
    ```bash
    kubectl delete pod nginx-cka01-trb
    ```
    
5. **Recreate the pod with the corrected YAML**:
    
    ```bash
    kubectl apply -f /tmp/test.yaml
    ```
    

Check if the pod is now running:

```bash
kubectl get pods
```

---

### **Step 6: Verify Web Application Accessibility**

If the pod is running but the application is **not accessible**, test with:

```bash
curl http://cluster1-controlplane:30001
```

If you get:

```
curl: (7) Failed to connect to cluster1-controlplane port 30001: Connection refused
```

Then the **Service configuration** might be incorrect.

---

### **Step 7: Fix the Service Selector**

Check the service configuration:

```bash
kubectl edit svc nginx-service-cka01-trb
```

Look for the **selector** section:

```yaml
selector:
  app: httpd-app-cka01-trb  # Incorrect
```

Change it to:

```yaml
selector:
  app: nginx-app-cka01-trb  # Corrected
```

Save and exit.

---

### **Step 8: Final Verification**

1. **Ensure the service is correctly exposing the application:**
    
    ```bash
    kubectl get svc nginx-service-cka01-trb
    ```
    
    Expected output:
    
    ```
    NAME                      TYPE       CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
    nginx-service-cka01-trb   NodePort   10.109.249.227   <none>        80:30001/TCP   20m
    ```
    
2. **Test the application again:**
    
    ```bash
    curl http://cluster1-controlplane:30001
    ```
    
    Expected output:
    
    ```
    <html>
    <head><title>Welcome to nginx!</title></head>
    ...
    </html>
    ```
    
    If you see the **Nginx default page**, the issue is resolved.

---

## **Summary**

✅ **Set context** to `cluster1`.  
✅ **Fixed image pull failure** (`nginx:latst` → `nginx:latest`).  
✅ **Corrected log file paths** for the `logs-container` (`/var/log/httpd/` → `/var/log/nginx/`).  
✅ **Fixed service selector** (`app: httpd-app-cka01-trb` → `app: nginx-app-cka01-trb`).  
✅ **Verified application accessibility** with `curl http://cluster1-controlplane:30001`.

