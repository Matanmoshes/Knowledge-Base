# Question


For this question, please set the context to cluster3 by running:

```
kubectl config use-context cluster3
```

Modify the existing web-gateway on the `cka5673` namespace to handle HTTPS traffic on port 443 for `kodekloud.com`, using a TLS certificate stored in a secret named kodekloud-tls.


---

# Guide and Explanation

## 1. Switch Context to Cluster3

The question specifically instructs to set your context to **cluster3**:

```bash
kubectl config use-context cluster3
```

This ensures all subsequent `kubectl` commands apply to **cluster3** rather than another cluster.

---

## 2. Check the Existing Gateway Configuration

List the current gateway YAML:

```bash
kubectl get gateway web-gateway -n cka5673 -o yaml
```

You might see something like:

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  name: web-gateway
  namespace: cka5673
spec:
  gatewayClassName: kodekloud
  listeners:
    - name: https
      port: 80
      protocol: HTTP
      allowedRoutes:
        namespaces:
          from: Same
```

Here, the **port** is **80** and the **protocol** is **HTTP** – which is not what we want for TLS.

---

## 3. Prepare the Updated Manifest

You’ll need to update:

1. **`port`** to **443**
2. **`protocol`** to **HTTPS**
3. **`hostname`** to **kodekloud.com** (or whichever domain you want)
4. Add the **TLS section** referencing your TLS secret **`kodekloud-tls`**

A corrected sample manifest could be:

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  name: web-gateway
  namespace: cka5673
spec:
  gatewayClassName: kodekloud
  listeners:
    - name: https
      protocol: HTTPS
      port: 443
      hostname: kodekloud.com
      tls:
        # This references the TLS secret, which must exist in the same namespace.
        certificateRefs:
          - name: kodekloud-tls
        mode: Terminate  # Typically 'Terminate' is used for TLS
      allowedRoutes:
        namespaces:
          from: Same
```

> **Note**: Depending on the Gateway controller you’re using, you might also need to specify `kind: Secret` and `group: core`. For example:
> 
> ```yaml
> certificateRefs:
> - kind: Secret
>   group: core
>   name: kodekloud-tls
> ```

---

## 4. Apply the New Manifest

Save the YAML above to a file, say `web-gateway-update.yaml`, then apply:

```bash
kubectl apply -f web-gateway-update.yaml
```

Check if it’s updated:

```bash
kubectl get gateway web-gateway -n cka5673 -o yaml
```

You should see port **443** and protocol **HTTPS** under `listeners`.

---

## 5. Confirm the Secret Exists

Ensure the secret `kodekloud-tls` is present in **cka5673**:

```bash
kubectl get secret -n cka5673
```

You should see:

```
NAME             TYPE                DATA   AGE
kodekloud-tls    kubernetes.io/tls   2      ...
```

The secret must contain a valid **TLS certificate** and **private key** in the fields `tls.crt` and `tls.key`.

---

## 6. Verify the Gateway Status

```bash
kubectl describe gateway web-gateway -n cka5673
```

Look for conditions such as:

```
Status:
  conditions:
    - type: Accepted
      status: "True"
    - type: Programmed
      status: "True"
```

These indicate that the Gateway is accepted and configured by the controller.

---

## 7. Test HTTPS Access

**How** you test will depend on how the Gateway is exposed externally. You might have:

- A LoadBalancer IP or DNS name for your Gateway.
- A NodePort or other service mechanism.

Once you have a **public IP** or a way to connect, you can do:

```bash
curl -k https://kodekloud.com
```

(substitute your actual domain or IP) and see if you get a response via HTTPS.  
If the certificate is recognized for `kodekloud.com`, you should see no TLS errors (unless your system doesn’t trust the CA, in which case you might see a warning).

---

## Recap

- **Context**: Switched to `cluster3` (`kubectl config use-context cluster3`).
- **Current Gateway**: Found it using `port: 80` and `protocol: HTTP`.
- **Updated** the listener to `port: 443`, `protocol: HTTPS`, set `hostname: kodekloud.com`, and referenced the secret `kodekloud-tls`.
- **Applied** changes and **verified** the Gateway status.
- **Tested** HTTPS connectivity using the domain or IP associated with that gateway.

This completes the process of adding TLS termination on port **443** for `kodekloud.com` using a TLS secret in the `cka5673` namespace.