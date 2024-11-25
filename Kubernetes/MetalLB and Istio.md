# Guide to Setting Up MetalLB and Istio on Your Kubernetes Cluster


## 1. Prerequisites

Before proceeding, ensure you have the following:

- A functioning Kubernetes cluster (version 1.29.11 as per your setup) with at least **three worker nodes**.
- `kubectl` configured to interact with your cluster.
- Helm installed on your local machine.
- `openssl` installed for generating secrets.

## 2. Node Preparation

Ensure your worker nodes are properly labeled to allow MetalLB and other components to schedule pods appropriately.

### 2.1 Verify Node Labels

List all nodes with their labels:

```bash
kubectl get nodes --show-labels
```

**Sample Output:**

```plaintext
NAME            STATUS   ROLES           AGE    VERSION    LABELS
ip-10-0-1-119   Ready    <none>          4h2m   v1.29.11   beta.kubernetes.io/arch=amd64,...
ip-10-0-1-172   Ready    control-plane   4h2m   v1.29.11   beta.kubernetes.io/arch=amd64,...
ip-10-0-1-252   Ready    <none>          4h2m   v1.29.11   beta.kubernetes.io/arch=amd64,...
ip-10-0-1-61    Ready    <none>          4h2m   v1.29.11   beta.kubernetes.io/arch=amd64,...
```

### 2.2 Label Worker Nodes

Label your worker nodes to differentiate them from control plane nodes:

```bash
kubectl label node ip-10-0-1-119 node-role.kubernetes.io/worker=""
kubectl label node ip-10-0-1-252 node-role.kubernetes.io/worker=""
kubectl label node ip-10-0-1-61 node-role.kubernetes.io/worker=""
```

**Verify Labels:**

```bash
kubectl get nodes --show-labels
```

Ensure the worker nodes have the `node-role.kubernetes.io/worker=""` label.

---

## 3. Installing MetalLB

MetalLB provides a network load-balancer implementation for Kubernetes, enabling services of type `LoadBalancer` in environments that lack native support.

### 3.1 Clean Up Previous Installations

Before a fresh installation, ensure all previous MetalLB resources are removed.

#### 3.1.1 Delete Existing MetalLB Namespace

```bash
kubectl delete namespace metallb-system --grace-period=0 --force
```

**Note:** Use `--force` and `--grace-period=0` cautiously as it forcibly deletes the namespace.

#### 3.1.2 Remove Remaining CRDs and Webhooks

List and delete MetalLB CRDs:

```bash
kubectl get crds | grep metallb
```

Delete each listed CRD:

```bash
kubectl delete crd <crd-name>
```

For example:

```bash
kubectl delete crd bfdprofiles.metallb.io
kubectl delete crd bgpadvertisements.metallb.io
kubectl delete crd bgppeers.metallb.io
kubectl delete crd communities.metallb.io
kubectl delete crd ipaddresspools.metallb.io
kubectl delete crd l2advertisements.metallb.io
kubectl delete crd servicel2statuses.metallb.io
```

Delete the MetalLB webhook configuration:

```bash
kubectl delete validatingwebhookconfiguration metallb-webhook-configuration
```

#### 3.1.3 Ensure All MetalLB Pods Are Deleted

List any lingering MetalLB pods:

```bash
kubectl get pods --all-namespaces | grep metallb
```

Force delete any remaining pods:

```bash
kubectl delete pod <pod-name> -n metallb-system --grace-period=0 --force
```

**Example:**

```bash
kubectl delete pod speaker-9tfct -n metallb-system --grace-period=0 --force
```

#### 3.1.4 Terminate Any Remaining MetalLB Processes on Nodes

SSH into each worker node and ensure no MetalLB processes are running:

```bash
sudo netstat -tulpn | grep -E ':(7472|7473|7946)'
```

If any processes are found (e.g., `speaker`), terminate them:

```bash
sudo kill <process-id>
```

**Example:**

```bash
sudo kill 143616
```

---

### 3.2 Install MetalLB Using Manifests

Install MetalLB using the official manifests.

#### 3.2.1 Apply MetalLB Manifest

```bash
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.14.8/config/manifests/metallb-native.yaml
```

**Expected Output:**

```plaintext
namespace/metallb-system created
customresourcedefinition.apiextensions.k8s.io/bfdprofiles.metallb.io created
...
serviceaccount/controller created
serviceaccount/speaker created
...
daemonset.apps/speaker created
validatingwebhookconfiguration.admissionregistration.k8s.io/metallb-webhook-configuration created
```

### 3.3 Configure MetalLB

#### 3.3.1 Create the `memberlist` Secret

MetalLB uses the `memberlist` protocol for internal communication between `speaker` pods.

```bash
kubectl create secret generic -n metallb-system memberlist --from-literal=secretkey="$(openssl rand -base64 128)"
```

**If the secret already exists, delete and recreate it:**

```bash
kubectl delete secret memberlist -n metallb-system
kubectl create secret generic -n metallb-system memberlist --from-literal=secretkey="$(openssl rand -base64 128)"
```

**Verify the Secret:**

```bash
kubectl describe secret memberlist -n metallb-system
```

**Expected Output:**

```plaintext
Name:         memberlist
Namespace:    metallb-system
Type:         Opaque

Data
====
secretkey:  174 bytes
```

#### 3.3.2 Create an `IPAddressPool`

Define the range of IP addresses that MetalLB can assign to `LoadBalancer` services.

**Create `metallb-ipaddresspool.yaml`:**

```yaml
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: default-addresspool
  namespace: metallb-system
spec:
  addresses:
  - 10.0.1.200-10.0.1.210  # Update this range to match your available IPs
```

**Apply the Configuration:**

```bash
kubectl apply -f metallb-ipaddresspool.yaml
```

#### 3.3.3 Create an `L2Advertisement`

Configure MetalLB to announce IPs using Layer 2 mode.

**Create `metallb-l2advertisement.yaml`:**

```yaml
apiVersion: metallb.io/v1beta1
kind: L2Advertisement
metadata:
  name: default-l2advertisement
  namespace: metallb-system
spec:
  ipAddressPools:
  - default-addresspool
```

**Apply the Configuration:**

```bash
kubectl apply -f metallb-l2advertisement.yaml
```

### 3.4 Verify MetalLB Installation

#### 3.4.1 Check MetalLB Pods

```bash
kubectl get pods -n metallb-system
```

**Expected Output:**

```plaintext
NAME                           READY   STATUS    RESTARTS   AGE
controller-77676c78d9-qmncn    1/1     Running   0          10m
speaker-5bxm8                  1/1     Running   0          10m
speaker-c22gc                  1/1     Running   0          10m
...
```

All `controller` and `speaker` pods should be in the `Running` state.

#### 3.4.2 Check Services

Ensure that the MetalLB webhook service is running:

```bash
kubectl get svc -n metallb-system
```

**Expected Output:**

```plaintext
NAME                   TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
metallb-webhook-service ClusterIP   10.96.0.1       <none>        443/TCP   10m
```

### 3.5 Testing MetalLB

Deploy a test application to verify that MetalLB assigns an external IP correctly.

#### 3.5.1 Deploy Nginx Test Deployment

```bash
kubectl create deployment nginx-test --image=nginx
```

#### 3.5.2 Expose the Deployment via LoadBalancer

```bash
kubectl expose deployment nginx-test --port=80 --type=LoadBalancer
```

#### 3.5.3 Verify the Service

```bash
kubectl get svc nginx-test
```

**Expected Output:**

```plaintext
NAME         TYPE           CLUSTER-IP       EXTERNAL-IP     PORT(S)        AGE
nginx-test   LoadBalancer   10.96.123.45     10.0.1.200      80:31234/TCP   1m
```

- The `EXTERNAL-IP` should be within the range `10.0.1.200-10.0.1.210`.

#### 3.5.4 Test Access to the Application

From a machine that can reach the cluster network:

```bash
curl http://10.0.1.200
```

You should see the Nginx welcome page HTML.


---

## 4. Installing Istio

Istio is a powerful service mesh that provides features like traffic management, security, and observability for microservices.

### 4.1 Install Istio Using Istioctl

#### 4.1.1 Download Istio

Visit the [Istio Releases](https://github.com/istio/istio/releases) page and download the desired version. Replace `1.24.0` with the latest stable version if necessary.

```bash
curl -L https://istio.io/downloadIstio | sh -
cd istio-1.24.0
export PATH=$PWD/bin:$PATH
```

#### 4.1.2 Install Istio

Use `istioctl` to install Istio with default profiles.

```bash
istioctl install --set profile=default -y
```

**Verify Installation:**

```bash
kubectl get pods -n istio-system
```

All Istio pods should be in the `Running` state.

### 4.2 Configure Istio Ingress Gateway

Istio uses an ingress gateway to manage inbound traffic to the mesh.

#### 4.2.1 Expose the Ingress Gateway

Create a service of type `LoadBalancer` for the Istio ingress gateway.

**Create `istio-ingressgateway.yaml`:**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: istio-ingressgateway
  namespace: istio-system
spec:
  type: LoadBalancer
  selector:
    istio: ingressgateway
  ports:
  - name: http
    port: 80
    targetPort: 8080
  - name: https
    port: 443
    targetPort: 8443
```

**Apply the Configuration:**

```bash
kubectl apply -f istio-ingressgateway.yaml
```

**Verify the Service:**

```bash
kubectl get svc istio-ingressgateway -n istio-system
```

An `EXTERNAL-IP` should be assigned by MetalLB within your configured IP range.

### 4.3 Deploy a Sample Application

Deploy a sample application to test Istio's traffic management.

#### 4.3.1 Deploy Bookinfo Application

Istio provides the Bookinfo sample application for testing.

```bash
kubectl apply -f samples/bookinfo/platform/kube/bookinfo.yaml
```

**Verify Deployment:**

```bash
kubectl get pods
```

All Bookinfo pods should be `Running`.

### 4.4 Verify Istio Installation

#### 4.4.1 Create Istio Gateway and VirtualService

**Create `bookinfo-gateway.yaml`:**

```yaml
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
  name: bookinfo-gateway
  namespace: default
spec:
  selector:
    istio: ingressgateway # use istio default ingress gateway
  servers:
  - port:
      number: 80
      name: http
      protocol: HTTP
    hosts:
    - "*"
---
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: bookinfo
  namespace: default
spec:
  hosts:
  - "*"
  gateways:
  - bookinfo-gateway
  http:
  - match:
    - uri:
        prefix: /productpage
    route:
    - destination:
        host: productpage
        port:
          number: 9080
  - match:
    - uri:
        prefix: /login
    route:
    - destination:
        host: productpage
        port:
          number: 9080
  - match:
    - uri:
        prefix: /logout
    route:
    - destination:
        host: productpage
        port:
          number: 9080
  - match:
    - uri:
        prefix: /api/v1/products
    route:
    - destination:
        host: reviews
        port:
          number: 9080
```

**Apply the Gateway and VirtualService:**

```bash
kubectl apply -f bookinfo-gateway.yaml
```

#### 4.4.2 Access the Application

Obtain the external IP of the Istio ingress gateway:

```bash
kubectl get svc istio-ingressgateway -n istio-system
```

**Sample Output:**

```plaintext
NAME                   TYPE           CLUSTER-IP       EXTERNAL-IP     PORT(S)        AGE
istio-ingressgateway   LoadBalancer   10.96.0.1        10.0.1.205      80:30413/TCP,443:32363/TCP   10m
```

Access the Bookinfo application by navigating to `http://<EXTERNAL-IP>/productpage` in your browser.

**Example:**

```plaintext
http://10.0.1.205/productpage
```

You should see the Bookinfo application interface.

---

# Reference:

- https://makeoptim.com/en/service-mesh/kubeadm-kubernetes-istio-setup/#metric-server