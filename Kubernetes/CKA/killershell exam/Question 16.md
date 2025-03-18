Solve this question on: `ssh cka5774`

The CoreDNS configuration in the cluster needs to be updated:

1. Make a backup of the existing configuration Yaml and store it at `/opt/course/16/coredns_backup.yaml`. You should be able to fast recover from the backup
2. Update the CoreDNS configuration in the cluster so that DNS resolution for `SERVICE.NAMESPACE.custom-domain` will work exactly like and in addition to `SERVICE.NAMESPACE.cluster.local`

Test your configuration for example from a _Pod_ with `busybox:1` image. These commands should result in an IP address:

```bash
nslookup kubernetes.default.svc.cluster.local
nslookup kubernetes.default.svc.custom-domain
```

---

## Solution

Below is a **step‐by‐step** way to back up the current **CoreDNS** ConfigMap, then update it so that **`<service>.<namespace>.custom-domain`** resolves the same as **`<service>.<namespace>.cluster.local`**. Finally, test from a Pod running busybox.

---

## 1. SSH into cka5774

```bash
ssh cka5774
```

---

## 2. Backup the Current CoreDNS Configuration

```bash
kubectl get cm coredns -n kube-system -o yaml > /opt/course/16/coredns_backup.yaml
```

Check the file:

```bash
cat /opt/course/16/coredns_backup.yaml
```

This ensures you can quickly revert if needed.

---

## 3. Update the CoreDNS Config

Edit the `coredns` ConfigMap in the `kube-system` namespace:

```bash
kubectl edit cm coredns -n kube-system
```

In the data section, you’ll see something like:

```yaml
data:
  Corefile: |
    .:53 {
        errors
        health
        kubernetes cluster.local in-addr.arpa ip6.arpa {
            pods insecure
            fallthrough in-addr.arpa,pod.cluster.local
            ttl 30
        }
        # ...
    }
```

To enable resolution for both `cluster.local` **and** `custom-domain`, simply add `custom-domain` next to `cluster.local`:

```diff
    kubernetes cluster.local in-addr.arpa ip6.arpa {
+       kubernetes cluster.local custom-domain in-addr.arpa ip6.arpa {
            pods insecure
            fallthrough in-addr.arpa,pod.cluster.local
            ttl 30
        }
```

**Alternatively**, if your block is separated by lines, you can just **append** `custom-domain` after `cluster.local` in the same line:

```yaml
kubernetes cluster.local custom-domain in-addr.arpa ip6.arpa {
  pods insecure
  fallthrough in-addr.arpa,pod.cluster.local
  ttl 30
}
```

Save and exit. CoreDNS will restart automatically, picking up the new config.

---

## 4. Test the Configuration

1. **Create a test Pod** with `busybox:1` (or `busybox:latest`), for example:
    
    ```bash
    kubectl run testbox --image=busybox:1 --restart=Never -it -- /bin/sh
    ```
    
    (In some environments, add `-n <namespace>` if needed.)
    
2. **Inside** the `testbox` shell, run:
    
    ```bash
    nslookup kubernetes.default.svc.cluster.local
    nslookup kubernetes.default.svc.custom-domain
    ```
    
    Both should return an IP (the cluster IP of the `kubernetes` Service).
    

If both queries succeed, your custom domain is working as intended.

---

## 5. Summary

You have:

1. **Backed up** the existing CoreDNS config to `/opt/course/16/coredns_backup.yaml`.
2. **Added** `custom-domain` to the `kubernetes` block in the **Corefile**.
3. **Verified** that both `cluster.local` and `custom-domain` addresses resolve the same way from within the cluster.