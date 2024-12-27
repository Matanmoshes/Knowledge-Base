
## JSON PATH
**Question**
Use JSON PATH query to retrieve the `osImages` of all the nodes and store it in a file `/opt/outputs/nodes_os_x43kj56.txt`.
The `osImage` are under the `nodeInfo` section under `status` of each node.

**Answer**
To retrieve the `osImage` of all nodes using a JSON Path query and store it in the specified file `/opt/outputs/nodes_os_x43kj56.txt`, you can use the following command:

```bash
kubectl get nodes -o jsonpath='{range .items[*].status.nodeInfo}{.osImage}{"\n"}{end}' > /opt/outputs/nodes_os_x43kj56.txt
```

### Explanation of the Command:

- `kubectl get nodes`: Retrieves information about all nodes.
- `-o jsonpath`: Specifies that the output should be formatted using a JSONPath query.
- `{range .items[*].status.nodeInfo}`: Iterates over all nodes and accesses the `nodeInfo` section under `status`.
- `{.osImage}`: Extracts the value of `osImage` for each node.
- `{"\n"}`: Adds a newline after each `osImage` for readability.
- `> /opt/outputs/nodes_os_x43kj56.txt`: Redirects the output to the specified file.

### Verify the Output:

1. **Check the contents of the file:**
    
    ```bash
    cat /opt/outputs/nodes_os_x43kj56.txt
    ```
    
    It should display the `osImage` values, one per line.
    
2. **Ensure the file exists and is correctly saved:**
    
    ```bash
    ls -l /opt/outputs/nodes_os_x43kj56.txt
    ```
    

This command will correctly extract the `osImage` values from all nodes and save them in the desired file.

---

## PV

Create a `Persistent Volume` with the given specification: -  
**Volume name**: `pv-analytics`  
**Storage**: `100Mi`  
**Access mode**: `ReadWriteMany`  
**Host path**: `/pv/data-analytics`


To create a Persistent Volume (PV) with the specified requirements, follow these steps:
### YAML Manifest

Create a YAML file `pv-analytics.yaml` with the following content:

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-analytics
spec:
  capacity:
    storage: 100Mi
  accessModes:
    - ReadWriteMany
  hostPath:
    path: /pv/data-analytics
```

### Explanation of the YAML

1. **`apiVersion: v1`**: Specifies the API version for the resource.
2. **`kind: PersistentVolume`**: Declares the resource as a PersistentVolume.
3. **`metadata.name: pv-analytics`**: Names the PV as `pv-analytics`.
4. **`spec.capacity.storage: 100Mi`**: Allocates 100Mi of storage.
5. **`spec.accessModes: ReadWriteMany`**: Allows multiple nodes to read/write simultaneously.
6. **`spec.hostPath.path: /pv/data-analytics`**: Specifies the directory on the host node that will back the PV.

---

### Apply the Manifest

Run the following command to create the Persistent Volume:

```bash
kubectl apply -f pv-analytics.yaml
```

---

### Verify the PV

Check the status of the Persistent Volume to ensure it was created successfully:

```bash
kubectl get pv pv-analytics
```

You should see output similar to this:

```plaintext
NAME           CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE
pv-analytics   100Mi      RWX            Retain           Available           <none>                  <age>
```

---

### Notes

- The PV will remain in the `Available` state until it is bound to a Persistent Volume Claim (PVC).
    
- Ensure the directory `/pv/data-analytics` exists on the node. If it doesn't, create it:
    
    ```bash
    sudo mkdir -p /pv/data-analytics
    ```

---


Task

Create a new user called `john`. Grant him access to the cluster. John should have permission to `create, list, get, update and delete pods` in the `development` namespace . The private key exists in the location: `/root/CKA/john.key` and csr at `/root/CKA/john.csr`.  

`Important Note`: As of kubernetes 1.19, the CertificateSigningRequest object expects a `signerName`.

### 1. **Create the CertificateSigningRequest (CSR)**

Generate the CSR YAML file, using the base64-encoded value of `/root/CKA/john.csr`:

```bash
base64 /root/CKA/john.csr | tr -d '\n'
```

Use the output from the above command and create the CSR YAML (`csr.yaml`):

```yaml
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: john-developer
spec:
  request: <base64-encoded-CSR-content>
  signerName: kubernetes.io/kube-apiserver-client
  usages:
  - client auth
```

Replace `<base64-encoded-CSR-content>` with the output from the `base64` command.

Apply the CSR:

```bash
kubectl apply -f csr.yaml
```

---

### 2. **Approve the CSR**

Approve the CSR for `john`:

```bash
kubectl certificate approve john-developer
```

Retrieve the signed certificate and save it to `/root/CKA/john.crt`:

```bash
kubectl get csr john-developer -o jsonpath='{.status.certificate}' | base64 -d > /root/CKA/john.crt
```

---

### 3. **Grant Permissions to `john`**

Create a Role named `developer` in the `development` namespace to allow CRUD operations on Pods:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: development
  name: developer
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["create", "list", "get", "update", "delete"]
```

Save this to `developer-role.yaml` and apply it:

```bash
kubectl apply -f developer-role.yaml
```

Create a RoleBinding to bind the `developer` role to the `john` user:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  namespace: development
  name: john-developer-binding
subjects:
- kind: User
  name: john
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: developer
  apiGroup: rbac.authorization.k8s.io
```

Save this to `john-rolebinding.yaml` and apply it:

```bash
kubectl apply -f john-rolebinding.yaml
```

---

### 4. **Create a Kubeconfig File for `john`**

Generate a kubeconfig file for `john`:

```bash
kubectl config set-credentials john \
  --client-key=/root/CKA/john.key \
  --client-certificate=/root/CKA/john.crt \
  --embed-certs=true

kubectl config set-context john-context \
  --cluster=kubernetes \
  --namespace=development \
  --user=john

kubectl config use-context john-context
```

---

### 5. **Verification**

Log in as `john` and verify permissions:

```bash
kubectl auth can-i create pods --namespace=development --as=john
kubectl auth can-i list pods --namespace=development --as=john
```

Both commands should return `yes`, confirming `john` has the required permissions.

---

This setup creates the user `john`, approves their certificate, and grants them the appropriate permissions in the `development` namespace.