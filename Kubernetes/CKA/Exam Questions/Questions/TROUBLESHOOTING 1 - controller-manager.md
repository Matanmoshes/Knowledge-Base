# Question


For this question, please set the context to cluster4 by running:
```
kubectl config use-context cluster4
```

On cluster4 we are having some weird issue where we intermittently get the following error while running kubectl commands:
```
The connection to the server cluster4-controlplane:6443 was refused - did you specify the right host or port?
```

Whenever you get this error, you can wait for 10-15 seconds and then kubectl works again, but it fails again after a few seconds.

We also noticed that kube-controller-manager-cluster4-controlplane pod is restarting continuously. Look into the issue and troubleshoot the same.

Note: After updating, system pods might take a little time to come in the running state, so wait a few minutes after making changes.

You can SSH into cluster4 by running:
```
ssh cluster4-controlplane
```

---

# Guide and Explanation

## 1. Context: Switch to cluster4

Start by ensuring your local `kubectl` context is set to **cluster4**:

```bash
kubectl config use-context cluster4
```

All subsequent `kubectl` commands will now refer to **cluster4**.

---

## 2. Check Pod Status in the kube-system Namespace

List all pods in `kube-system`:

```bash
kubectl get pods -n kube-system
```

You may see something like this:

```
NAME                                            READY   STATUS             RESTARTS   AGE
...
kube-controller-manager-cluster4-controlplane    0/1     CrashLoopBackOff   5         10m
kube-apiserver-cluster4-controlplane            0/1     CrashLoopBackOff   5         10m
...
```

This indicates that **kube-controller-manager** and/or **kube-apiserver** are failing repeatedly.

---

## 3. Investigate kube-controller-manager Logs

Check the logs for the crashing **kube-controller-manager**:

```bash
kubectl logs -f kube-controller-manager-cluster4-controlplane -n kube-system
```

A typical error might look like:

```
leaderelection.go:330] error retrieving resource lock kube-system/kube-controller-manager:
Get "https://<API-SERVER-IP>:6443/apis/coordination.k8s.io/v1/...": dial tcp <API-SERVER-IP>:6443: connect: connection refused
```

This indicates the controller manager can’t reliably reach the API server on port `6443`.

---

## 4. Investigate kube-apiserver Issues

Because the controller manager can’t talk to the API, the **kube-apiserver** might also be in a bad state. Check:

```bash
kubectl logs -f kube-apiserver-cluster4-controlplane -n kube-system
```

and also

```bash
kubectl get events --field-selector involvedObject.name=kube-apiserver-cluster4-controlplane -n kube-system
```

Often, you’ll see an event like:

```
Warning   Unhealthy   pod/kube-apiserver-cluster4-controlplane
Liveness probe failed: Get "https://<API-SERVER-IP>:6444/livez": dial tcp <API-SERVER-IP>:6444: connect: connection refused
```

This reveals the liveness probe is trying to connect to **port 6444** instead of **6443**.

---

## 5. Fix the kube-apiserver Manifest

1. **SSH** into the control plane node:
    
    ```bash
    ssh cluster4-controlplane
    ```
    
2. **Edit** the static pod manifest for the API server:
    
    ```bash
    sudo vi /etc/kubernetes/manifests/kube-apiserver.yaml
    ```
    
3. Find the **`livenessProbe`** section. You might see something like:
    
    ```yaml
    livenessProbe:
      httpGet:
        scheme: HTTPS
        port: 6444
        path: /livez
    ```
    
4. Change **`port: 6444`** to **`port: 6443`**:
    
    ```yaml
    livenessProbe:
      httpGet:
        scheme: HTTPS
        port: 6443
        path: /livez
    ```
    
5. **Save and exit**. Because this is a static pod, **kubelet** will detect the change and recreate the `kube-apiserver` pod with the correct liveness probe.

---

## 6. Wait and Verify

1. After a minute or so, run:
    
    ```bash
    kubectl get pods -n kube-system
    ```
    
    - **kube-apiserver-cluster4-controlplane** should transition to **Running**.
    - **kube-controller-manager-cluster4-controlplane** should also stabilize (Running, not restarting).
2. Try multiple `kubectl` commands:
    
    ```bash
    kubectl get nodes
    kubectl get pods -A
    ```
    
    If everything is correct, you **will not** see the “connection refused” error again.
    

---

# Summary

1. **Symptom**: Intermittent “connection refused” to `cluster4-controlplane:6443`.
2. **Root Cause**: The **kube-apiserver**’s liveness probe was pointed at **6444** instead of **6443**, causing it to fail its health check and restart repeatedly.
3. **Fix**: Correct the port in `/etc/kubernetes/manifests/kube-apiserver.yaml`.
4. **Result**: The API server and controller manager stabilize, no more “connection refused” issues with `kubectl`.

---

## Key Takeaways for CKA

- When **kube-controller-manager**, **kube-scheduler**, or **kube-apiserver** keep crashing, always **check their logs** and **kube-system events**.
- **Static pods** are located under `/etc/kubernetes/manifests/`. Any misconfiguration in those manifests leads to repeated restarts.
- Ensure **liveness probes** match the correct API server port (`6443` by default).
- After edits, wait for the system pods to restart and confirm they reach a stable **Running** status.

This pattern of **control plane troubleshooting** is common in real-world Kubernetes and can appear in exam questions. Practice investigating logs, events, and manifests for any sign of mismatched ports or addresses that cause recurring failures.