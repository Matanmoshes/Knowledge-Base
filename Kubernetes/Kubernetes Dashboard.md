## Detailed Guide: Installing and Using Kubernetes Dashboard

The **Kubernetes Dashboard** is a web-based UI for managing Kubernetes clusters. This guide will walk you through the steps to install, configure, and use the Kubernetes Dashboard, including installing the Metrics Server for monitoring.

---

### **Step 1: Install Kubernetes Dashboard**

1. **Apply the Kubernetes Dashboard YAML:**
    
    ```bash
    kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.2.0/aio/deploy/recommended.yaml
    ```
    
2. **Verify Installation:** Use the following command to ensure the pods are running:
    
    ```bash
    kubectl get pods -n kubernetes-dashboard -w
    ```
    
    Expected output:
    
    ```plaintext
    NAME                                         READY   STATUS    RESTARTS   AGE
    dashboard-metrics-scraper-79c5968bdc-w2gmc   1/1     Running   0          1m
    kubernetes-dashboard-9f9799597-w9fbz         1/1     Running   0          1m
    ```
    

---

### **Step 2: Install Metrics Server (Optional but Recommended)**

The **Metrics Server** is needed to display resource usage metrics in the dashboard.

1. **Install Metrics Server:**
    
    ```bash
    kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
    ```
    
2. **Edit the Metrics Server Deployment:**
    
    ```bash
    kubectl edit deploy -n kube-system metrics-server
    ```
    
    Add the following argument under `spec.template.spec.containers.args`:
    
    ```yaml
    - --kubelet-insecure-tls
    ```
    
3. **Verify Metrics Server is Running:**
    
    ```bash
    kubectl get pod -n kube-system -w
    ```
    
4. **Test Metrics Server:** Check metrics using the `kubectl top` command:
    
    ```bash
    kubectl top nodes
    ```
    
    Example output:
    
    ```plaintext
    NAME     CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%
    master   242m         12%    2153Mi          56%
    node1    143m         7%     2158Mi          56%
    node2    99m          4%     1665Mi          43%
    ```
    

---

### **Step 3: Expose Kubernetes Dashboard**

1. **Start a Proxy to Access the Dashboard:**
    
    ```bash
    kubectl proxy
    ```
    
2. **Access the Dashboard:** Open your browser and navigate to:
    
    ```
    http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
    ```
    

---

### **Step 4: Create an Admin User for Dashboard**

To log in to the Kubernetes Dashboard, create a service account and grant it admin permissions.

1. **Create a Service Account:**
    
```bash
    cat <<EOF | kubectl apply -f -
    apiVersion: v1
    kind: ServiceAccount
    metadata:
      name: admin-user
      namespace: kubernetes-dashboard
    EOF
```
    
2. **Bind the Service Account to Cluster Admin Role:**
    
```bash
    cat <<EOF | kubectl apply -f -
    apiVersion: rbac.authorization.k8s.io/v1
    kind: ClusterRoleBinding
    metadata:
      name: admin-user
    roleRef:
      apiGroup: rbac.authorization.k8s.io
      kind: ClusterRole
      name: cluster-admin
    subjects:
    - kind: ServiceAccount
      name: admin-user
      namespace: kubernetes-dashboard
    EOF
```
    
3. **Generate a Bearer Token:**
    
```bash
    kubectl -n kubernetes-dashboard create token admin-user
```
    
    Copy the generated token.
    

---

### **Step 5: Log in to Kubernetes Dashboard**

1. Open the Kubernetes Dashboard URL:
    
```
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
```
    
2. Select **Token** as the login method.
    
3. Paste the token you generated in the previous step into the **Enter token** field.
    
4. Click **Sign in**.
    

---

### **Step 6: Using Kubernetes Dashboard**

Once logged in, you can:

- View the current state of workloads (Deployments, ReplicaSets, Pods).
- Manage cluster resources (ConfigMaps, Secrets, and Storage).
- View resource usage metrics (if Metrics Server is installed).
- Debug workloads and monitor events.

---

### **Troubleshooting**

1. **Dashboard Not Accessible:**
    
    - Ensure the proxy is running: `kubectl proxy`.
    - Verify the Kubernetes Dashboard pods are running.
2. **Metrics Not Available:**
    
    - Verify Metrics Server is running: `kubectl get pods -n kube-system | grep metrics-server`.
    - Check Metrics Server logs for errors:
        
```bash
        kubectl logs -n kube-system deployment/metrics-server
```
        
3. **Insufficient Permissions:**
    
    - Ensure the admin service account and ClusterRoleBinding are properly configured.

---

