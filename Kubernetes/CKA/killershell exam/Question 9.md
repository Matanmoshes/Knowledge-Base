Solve this question on: `ssh cka9412`

There is _ServiceAccount_ `secret-reader` in _Namespace_ `project-swan`. Create a _Pod_ of image `nginx:1-alpine` named `api-contact` which uses this _ServiceAccount_.

Exec into the _Pod_ and use `curl` to manually query all _Secrets_ from the Kubernetes Api.

Write the result into file `/opt/course/9/result.json`.

---

## Solution

Below is a **step‐by‐step** approach to solve this question on **`cka9412`**, creating a Pod that uses the `secret-reader` ServiceAccount in `project-swan` and manually querying all secrets from the Kubernetes API, saving the output to `/opt/course/9/result.json`.

---

## 1. SSH into cka9412

```bash
ssh cka9412
```

---

## 2. Create the Pod Spec

1. **Namespace**: `project-swan`
2. **Pod Name**: `api-contact`
3. **Image**: `nginx:1-alpine`
4. **ServiceAccountName**: `secret-reader` (already exists per the question)

Create a file, for example `api-contact.yaml`:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: api-contact
  namespace: project-swan
spec:
  serviceAccountName: secret-reader
  containers:
    - name: main
      image: nginx:1-alpine
      command: ["sleep", "9999999"]
```

Apply it:

```bash
kubectl apply -f api-contact.yaml
```

Wait until the Pod is `Running`:

```bash
kubectl get pod -n project-swan
```

---

## 3. Install `curl` Inside the Pod (Alpine)

The base `nginx:1-alpine` image might not have `curl`, so we’ll install it interactively or in one command:

```bash
kubectl exec -n project-swan api-contact -- apk add --no-cache curl
```

---

## 4. Query All Secrets & Redirect to Local File

We will:

1. Use the **ServiceAccount token** from `/var/run/secrets/kubernetes.io/serviceaccount/token`.
2. Add the Bearer token to the `Authorization` header.
3. Query the **Kubernetes API** at `https://kubernetes.default.svc/api/v1/secrets`.
4. Store the JSON output **locally** on cka9412 at `/opt/course/9/result.json`.

Run this **on your control-plane host** (`cka9412`), capturing the command output in a file:

```bash
kubectl exec -n project-swan api-contact -- sh -c \
  'TOKEN=$(cat /var/run/secrets/kubernetes.io/serviceaccount/token); \
   curl -sSk --header "Authorization: Bearer $TOKEN" \
   https://kubernetes.default.svc/api/v1/secrets' \
> /opt/course/9/result.json
```

### Explanation

- **`kubectl exec -n project-swan api-contact -- sh -c '...'`** runs commands inside the container.
- **`TOKEN=$(cat /var/run/secrets/kubernetes.io/serviceaccount/token)`** loads the ServiceAccount token.
- **`curl -sSk`**:
    - `-s` silent
    - `-S` show errors if any
    - `-k` ignore certificate verification (since we’re hitting `kubernetes.default.svc` and might not have the CA certificate installed – or you can do `--cacert` if needed).
- **`> /opt/course/9/result.json`** on your local shell captures the standard output from `kubectl exec` into the file on the host.

---

## 5. Verify the File

Check that **`/opt/course/9/result.json`** exists and contains the API response:

```bash
cat /opt/course/9/result.json | jq .
```

_(assuming `jq` is available to pretty-print JSON. Otherwise just `cat`.)_

You should see the entire secrets listing from the cluster (subject to RBAC permissions of the `secret-reader` ServiceAccount).

That’s it! You now have:

- A Pod named `api-contact` using `secret-reader` in `project-swan`.
- A manual API query storing all secrets JSON to `/opt/course/9/result.json`.