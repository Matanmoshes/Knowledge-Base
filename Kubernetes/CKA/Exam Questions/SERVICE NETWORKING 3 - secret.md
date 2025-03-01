# Question 

We have deployed a `2-tier` web application on the `cluster3` nodes in the `canara-wl05` namespace. However, at the moment, the web app pod cannot establish a connection with the MySQL pod successfully.  
  
You can check the status of the application from the terminal by running the `curl` command with the following syntax:  

```sh
curl http://cluster3-controlplane:NODE-PORT
```

To make the application work, create a new secret called `db-secret-wl05` with the following key values: -  
**1.** DB_Host=mysql-svc-wl05  
**2.** DB_User=root  
**3.** DB_Password=password123  

Next, configure the web application pod to load the new environment variables from the newly created secret.  
**Note:** Check the web application again using the `curl` command, and the status of the application should be `success`.  
  
You can SSH into the `cluster3` using `ssh cluster3-controlplane` command.

---
## 1. Switch to the Correct Context

Make sure you are operating in **cluster3**:

```bash
kubectl config use-context cluster3
```

---

## 2. (Optional) Verify Nodes and SSH into Control Plane

1. Check that your nodes are up and running:
    
    ```bash
    kubectl get nodes -o wide
    ```
    
2. SSH into the cluster3 control plane node if needed:
    
    ```bash
    ssh cluster3-controlplane
    ```
    

---

## 3. Test the Current Status of the Web App

Use the `curl` command with **NODE-PORT** (adjust IP/port as needed for your environment). For example:

```bash
curl http://10.17.63.11:31020
```

You may see output like:

```html
...
<img src="/static/img/failed.png">
<h3>Failed connecting to the MySQL database.</h3>
<h2> Environment Variables: 
    DB_Host=Not Set; 
    DB_Database=Not Set; 
    DB_User=Not Set; 
    DB_Password=Not Set; 
    2003: Can't connect to MySQL server on 'localhost:3306' (111 Connection refused)
</h2>
```

This confirms the app cannot reach the MySQL database.

---

## 4. Create the Secret for Database Connection

We need a **new secret** named `db-secret-wl05` that stores the DB connection information. Run:

```bash
kubectl create secret generic db-secret-wl05 \
  -n canara-wl05 \
  --from-literal=DB_Host=mysql-svc-wl05 \
  --from-literal=DB_User=root \
  --from-literal=DB_Password=password123
```

---

## 5. Update the Web App Pod to Use the Secret

You have a Pod named `webapp-pod-wl05` in the `canara-wl05` namespace. Edit or create a YAML file (e.g., `webapp-pod-wl05.yaml`) so it includes:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: webapp-pod-wl05
  namespace: canara-wl05
  labels:
    run: webapp-pod-wl05
spec:
  containers:
  - image: kodekloud/simple-webapp-mysql
    name: webapp-pod-wl05
    envFrom:
    - secretRef:
        name: db-secret-wl05
```

**Key points**:

- `envFrom` → `secretRef` is set to `db-secret-wl05`.
- This loads `DB_Host`, `DB_User`, and `DB_Password` from that secret into the container environment variables.

---

## 6. Replace the Existing Pod

Use the **`kubectl replace`** command with `--force` to replace the existing pod. For example:

```bash
kubectl replace -f webapp-pod-wl05.yaml --force
```

This will terminate the old pod and create a new one with the correct environment variables.

---

## 7. Verify the Pod is Running

Check that the updated pod is running:

```bash
kubectl get pod -n canara-wl05
```

Wait until the status is `Running`.

---

## 8. Test the Application Again

Once the new pod is up, test via `curl` again:

```bash
curl http://10.17.63.11:31020
```

You should now see a **success** response, for example:

```html
<img src="/static/img/success.jpg">
<h3> Successfully connected to the MySQL database.</h3>
```

This indicates the web application successfully connected to the MySQL pod.

---

## Summary of Steps

1. **Switch context** to `cluster3`.
2. **Verify nodes** and **SSH** to the control plane if necessary.
3. **Curl** the NodePort to confirm the current “Failed” status.
4. **Create the secret** `db-secret-wl05` in `canara-wl05`.
5. **Update the webapp pod** YAML to pull environment variables from that secret.
6. **Replace** the pod using `kubectl replace --force`.
7. **Confirm** the pod is running.
8. **Curl** again to see the “success” status.
