Solve this question on: `ssh cka7968`

Install the MinIO Operator using Helm in _Namespace_ `minio`. Then configure and create the _Tenant_ CRD:

1. Create _Namespace_ `minio`
    
2. Install Helm chart `minio/operator` into the new _Namespace_. The Helm Release should be called `minio-operator`
    
3. Update the `Tenant` resource in `/opt/course/2/minio-tenant.yaml` to include `enableSFTP: true` under `features`
    
4. Create the `Tenant` resource from `/opt/course/2/minio-tenant.yaml`
    

> ℹ️ It is not required for MinIO to run properly. Installing the Helm Chart and the _Tenant_ resource as requested is enough

---

## Solution

Below is a **step‐by‐step** approach you can use **in an exam environment**, assuming:

1. You have **Helm** installed.
2. You have **kubeconfig** access but **no external internet** or direct knowledge of the MinIO repo link.
3. You do have the **Kubernetes documentation** and possibly the **`helm`** CLI help pages available.

The question wants you to:

- **Create a namespace** `minio`.
- **Install the MinIO Operator** into that namespace with Helm release name `minio-operator`.
- **Update the Tenant** YAML (found at `/opt/course/2/minio-tenant.yaml`) to enable SFTP, then **create** that Tenant resource.

---

## 1. Gather Information and Check Local Helm Environment

### 1.1 View Any Pre-Added Repositories

You can run:

```bash
helm repo list
```

to see if `minio` is already listed. If so, you don’t need the link. If not, you must **add** it.

### 1.2 Use the Helm CLI Help

In the exam, you can do:

```bash
helm repo add --help
```

to recall the syntax. This will show something like:

```
helm repo add [NAME] [URL] [flags]
```

### 1.3 Search for "minio/operator" in Existing Repos

```bash
helm search repo minio/operator
```

If your environment **already** has the repository added, you’ll see a match (e.g. `minio/operator 4.5.8` etc.). If not, it will return no results.

---

## 2. Add the MinIO Helm Repository

In many official references, the MinIO chart is at **`https://helm.min.io`**. But let’s assume you don’t remember that link. Here’s how you’d find it in a real environment:

- **`helm search hub minio`** (if `helm hub` plugin or feature is available – sometimes it’s not).
- Or you might see references in the official MinIO [docs](https://docs.min.io/docs/) – though that’s outside standard “Kubernetes docs.”
- If the exam environment provides a reference in a local doc, or you see a clue in the question (sometimes they do mention it), you can use that.

Let’s assume you discovered the chart is from `minio https://helm.min.io`. You’d do:

```bash
helm repo add minio https://helm.min.io
helm repo update
```

_(If you truly couldn’t find the link in the exam, typically the question itself or environment would provide it, or it might already be configured. But let’s proceed.)_

---

## 3. Create the Namespace

```bash
kubectl create namespace minio
```

Verify:

```bash
kubectl get ns minio
```

---

## 4. Install MinIO Operator via Helm

Now that the repository is added, do:

```bash
helm install minio-operator minio/operator \
  --namespace minio
```

- **`minio-operator`** → The helm release name.
- **`minio/operator`** → The chart.

Check:

```bash
helm list -n minio
```

You should see something like:

```
NAME            	NAMESPACE	REVISION	UPDATED          	STATUS  	CHART            	APP VERSION
minio-operator	minio     	1       	...              	deployed	operator-<ver>	<ver>
```

---

## 5. Update the Tenant YAML to Enable SFTP

The question says the **Tenant** resource is at `/opt/course/2/minio-tenant.yaml`. You need to add:

```yaml
features:
  enableSFTP: true
```

somewhere in the `spec` (under the tenant’s config). For example:

```yaml
apiVersion: minio.min.io/v2
kind: Tenant
metadata:
  name: my-tenant
  namespace: minio
spec:
  # ...
  features:
    enableSFTP: true
  # ...
```

_(The exact location depends on how the tenant manifest is structured.)_

---

## 6. Create the Tenant

Finally:

```bash
kubectl apply -f /opt/course/2/minio-tenant.yaml
```

Check:

```bash
kubectl get tenants -n minio
```

You should see your new tenant (e.g. `my-tenant`).

---

## 7. Confirm Installation (Optional)

```bash
kubectl get pods -n minio
```

Likely you’ll see `minio-operator-<something>` running, plus the new tenant pods if it’s fully deployed (depending on the tenant spec).

At this point, **MinIO Operator** is installed and the **Tenant** resource is created with **SFTP** enabled.

---

# Summary

1. **Create namespace** `minio`.
2. **Find or recall** `helm repo add minio https://helm.min.io`.
3. **helm install minio-operator minio/operator -n minio**
4. **Edit** `/opt/course/2/minio-tenant.yaml` to add `features.enableSFTP: true`.
5. **Apply** the tenant resource.

That meets all the requirements of the question.