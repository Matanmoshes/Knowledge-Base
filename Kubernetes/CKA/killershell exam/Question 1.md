Solve this question on: `ssh cka9412`

You're asked to extract the following information out of kubeconfig file `/opt/course/1/kubeconfig` on `cka9412`:

1. Write all kubeconfig context names into `/opt/course/1/contexts`, one per line
    
2. Write the name of the current context into `/opt/course/1/current-context`
    
3. Write the client-certificate of user `account-0027` base64-decoded into `/opt/course/1/cert`
---

## 1. SSH into cka9412

```bash
ssh cka9412
```

---

## 2. Write All Context Names into `/opt/course/1/contexts`

Use `kubectl config get-contexts` and output only the names:

```bash
kubectl config get-contexts --kubeconfig /opt/course/1/kubeconfig -o name \
  > /opt/course/1/contexts
```

**Explanation**

- **`--kubeconfig /opt/course/1/kubeconfig`** tells `kubectl` to read that kubeconfig file instead of the default.
- **`-o name`** outputs **only** context names, one per line.
- We **redirect** (`>`) to `/opt/course/1/contexts`.

---

## 3. Write the Current Context into `/opt/course/1/current-context`

```bash
kubectl config current-context --kubeconfig /opt/course/1/kubeconfig \
  > /opt/course/1/current-context
```

**Explanation**

- **`kubectl config current-context`** prints the context name that’s currently set as default in the provided kubeconfig.

---

## 4. Extract & Decode the `client-certificate` for the User **`account-0027`** into `/opt/course/1/cert`

We can parse the kubeconfig in JSON form and filter out `users[]` with the name `account-0027`. Then decode `client-certificate-data` from base64. One approach is to use **`kubectl config view -o json`** piped to **jq**:

```bash
kubectl config view \
  --kubeconfig /opt/course/1/kubeconfig \
  -o json \
  | jq -r '.users[] | select(.name=="account-0027") | .user."client-certificate-data"' \
  | base64 -d \
  > /opt/course/1/cert
```

**Explanation**

1. **`kubectl config view -o json`** → dump the kubeconfig as JSON.
2. **`jq -r '.users[] | select(.name=="account-0027") | .user."client-certificate-data"'`** → find the user with name `account-0027`, and output the `client-certificate-data` field.
3. **`base64 -d`** → decode the certificate from base64 to raw bytes.
4. **`> /opt/course/1/cert`** → write it to the file `cert`.

---

## 5. Verify the Results

1. **Contexts File**
    
    ```bash
    cat /opt/course/1/contexts
    ```
    
    You should see a list of context names, one per line.
    
2. **Current Context**
    
    ```bash
    cat /opt/course/1/current-context
    ```
    
    Should show exactly one context name (the default in `/opt/course/1/kubeconfig`).
    
3. **Certificate File**
    
    ```bash
    file /opt/course/1/cert
    ```
    
    or
    
    ```bash
    cat /opt/course/1/cert | openssl x509 -text -noout
    ```
    
    If it’s indeed an x509 certificate, you’ll see the decoded certificate details.
    

