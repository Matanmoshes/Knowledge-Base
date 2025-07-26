
Each task mimics the style, wording, and time pressure of real CKA items.  Do them in a **fresh lab cluster** with only the default namespaces and Helm v3.  Treat every command as if the grader will check both the Kubernetes resources **and** the file paths it asks you to create.

| **#**                             | **Task**                                                                                                                                                                                                                                                                                                                                                                                                                        |
| --------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **1. Add & Install**              | _Repository:_ Add the Bitnami repo at https://charts.bitnami.com/bitnami under the alias **bitnami**.  _Release:_ Install MySQL chart **version 9.14.3** as release **db** in namespace **data** (create the namespace).  Expose the service as **ClusterIP** only.                                                                                                                                                             |
| **2. Version Search & Pull**      | Using Helm **search**, show **all** versions of the grafana chart in repo **grafana** (https://grafana.github.io/helm-charts).  Pull **version 7.0.15** into /opt/charts/ and untar it there.  Do **not** install it.                                                                                                                                                                                                           |
| **3. Render to File**             | With the chart you saved in Task 2, render the manifests (no install) and write them to /opt/manifests/grafana-rendered.yaml.  Use release name **gviewer**, namespace **monitoring**.                                                                                                                                                                                                                                          |
| **4. Install with Custom Values** | From the Bitnami repo, install **nginx** chart **latest stable** as release **frontend** in namespace **web** with these overrides:• replicaCount=3• service type **NodePort** on port **30080**.Create the overrides inline with --set.                                                                                                                                                                                        |
| **5. Upgrade & Rollback**         | In the same namespace **web**, upgrade **frontend** to the **next minor version** of the nginx chart.  Then immediately roll back to the previous revision.  Show the release history.                                                                                                                                                                                                                                          |
| **6. CRD Prerequisite**           | Add repo **jetstack** (https://charts.jetstack.io).  Install **cert-manager** chart **v1.14.4** as release **cert-mgr** in namespace **cert-manager**.  Make sure the CRDs are applied (use the chart’s built-in CRD hook).  Verify the CRD certificates.cert-manager.io is _Established_.                                                                                                                                      |
| **7. Operator + CRD + Workload**  | From repo **prometheus-community** (https://prometheus-community.github.io/helm-charts):1. Install **kube-prometheus-stack** chart **46.8.0** as release **kp** in namespace **monitoring**.2. Wait for CRD prometheusrules.monitoring.coreos.com to establish.3. Using the _same_ repo, install **alertmanager** chart **0.25.0** as release **alert** in namespace **monitoring**.4. Show that Alertmanager pods are running. |
| **8. Local Chart Install**        | Create a skeleton chart named **demoapp** with helm create.  Change the values.yaml image tag to **2.0.0**.  Package the chart, then install it from the packaged .tgz into namespace **apps** as release **demo**.                                                                                                                                                                                                             |
| **9. Skip-CRD Scenario**          | A teammate already deployed the ingress-nginx CRDs cluster-wide.  Install a _second_ ingress-nginx chart **v4.10.0** into namespace **edge** as release **ing2** **without touching CRDs**.                                                                                                                                                                                                                                     |
| **10. Clean Removal & Proof**     | Uninstall all releases created in tasks 1–9, delete any namespaces you created, and list all remaining Helm releases (should be none).                                                                                                                                                                                                                                                                                          |

---


Work through each task until you can execute the entire sequence from memory and fix mistakes quickly—the exact muscle memory the CKA rewards.  Happy drilling!


---

Below is a **worked answer key** for the ten “CKA-level” Helm exercises you drafted, followed by general study techniques and mental check-lists that will help you solve similar problems in the exam’s restricted, no-internet environment.

---



## **Task-by-task solution set**

  

### **1  Add & Install**

```
# Add repo
$ helm repo add bitnami https://charts.bitnami.com/bitnami
$ helm repo update

# Create namespace and install
$ helm install db bitnami/mysql \
    --version 9.14.3 \
    --namespace data --create-namespace \
    --set service.type=ClusterIP
```

**Why**:

_Use_ _--set service.type=ClusterIP_ _rather than editing a values file to keep it one-liner and match the “ClusterIP only” requirement._

**Verify**

```
$ helm list -n data
$ kubectl -n data get svc db-mysql   # Should show TYPE ClusterIP
```

---

### **2  Version Search & Pull**

```
$ helm repo add grafana https://grafana.github.io/helm-charts
$ helm repo update

# Show all versions
$ helm search repo grafana/grafana --versions

# Pull target version
$ mkdir -p /opt/charts
$ helm pull grafana/grafana --version 7.0.15 --untar --untardir /opt/charts
```

**Why**:

_helm pull --untar_ _keeps the tarball but also expands it, which is exactly what the task asks (“pull … and untar it there”)._

---

### **3  Render to File (no install)**

```
$ mkdir -p /opt/manifests
$ helm template gviewer /opt/charts/grafana \
    --namespace monitoring \
    > /opt/manifests/grafana-rendered.yaml
```

**Why**:

_helm template_ _renders the manifests client-side without touching the cluster, suitable for offline inspection or GitOps pipelines._

---

### **4  Install with Custom Values**

```
$ helm install frontend bitnami/nginx \
    --namespace web --create-namespace \
    --set replicaCount=3 \
    --set service.type=NodePort \
    --set service.nodePorts.http=30080
```

**Verify replica count & port**

```
$ kubectl -n web get deploy frontend-nginx
$ kubectl -n web get svc frontend-nginx
```

---

### **5  Upgrade & Rollback**

```
# 5-a find next minor version (example: from 15.0.5 to 15.1.0)
$ helm search repo bitnami/nginx --versions | head

# 5-b upgrade
$ helm upgrade frontend bitnami/nginx \
    --namespace web \
    --version <next-minor-version> \
    --reuse-values        # keeps the overrides from task 4

# 5-c rollback
$ helm rollback frontend 1  --namespace web   # 1 = previous revision

# 5-d show history
$ helm history frontend -n web
```

**Tip**: helm history prints revision numbers; use them for rollback instead of guessing.

---

### **6  CRD Prerequisite – cert-manager**

```
$ helm repo add jetstack https://charts.jetstack.io
$ helm repo update

$ helm install cert-mgr jetstack/cert-manager \
    --namespace cert-manager --create-namespace \
    --version v1.14.4 \
    --set installCRDs=true
```

**Verify CRD condition**

```
$ kubectl get crd certificates.cert-manager.io -o jsonpath='{.status.conditions[?(@.type=="Established")].status}'
# Should return "True"
```

**Why**:

_The chart supports_ _installCRDs=true__; using it avoids a separate_ _kubectl apply -f__._

---

### **7  Operator + CRD + Workload chain**

```
$ helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
$ helm repo update

# 1. kube-prometheus-stack
$ helm install kp prometheus-community/kube-prometheus-stack \
    --version 46.8.0 \
    --namespace monitoring --create-namespace

# 2. Wait for CRD to be established (simple loop)
$ until kubectl get crd prometheusrules.monitoring.coreos.com; do sleep 2; done

# 3. alertmanager chart
$ helm install alert prometheus-community/alertmanager \
    --version 0.25.0 \
    --namespace monitoring

# 4. Verify Alertmanager pods
$ kubectl -n monitoring get pods -l app.kubernetes.io/name=alertmanager,app.kubernetes.io/instance=alert
```

**Why**:

_Some CRDs are created by kube-prometheus-stack. Waiting ensures the second chart does not fail due to missing types._

---

### **8  Local Chart Install**

```
# Generate skeleton
$ helm create demoapp
# Edit values.yaml (image.tag)
$ sed -i 's/tag: ""/tag: "2.0.0"/' demoapp/values.yaml

# Package
$ helm package demoapp   # creates demoapp-0.1.0.tgz

# Install from the packaged file
$ helm install demo ./demoapp-0.1.0.tgz \
    --namespace apps --create-namespace
```

---

### **9  Skip-CRD Scenario**

```
$ helm install ing2 ingress-nginx/ingress-nginx \
    --version 4.10.0 \
    --namespace edge --create-namespace \
    --skip-crds
```

**Why**:

_--skip-crds_ _tells Helm to ignore any_ _crds/_ _directory in the chart, avoiding accidental overwrites._

---

### **10  Clean Removal & Proof**

```
# List releases once to grab names if you forgot any
$ helm list -A

# Uninstall each (order doesn’t matter, show only tasks 1-9)
$ helm uninstall db -n data
$ helm uninstall frontend -n web
$ helm uninstall cert-mgr -n cert-manager
$ helm uninstall kp -n monitoring
$ helm uninstall alert -n monitoring
$ helm uninstall demo -n apps
$ helm uninstall ing2 -n edge

# Delete created namespaces (double-check you’re not deleting kube-system etc.)
$ kubectl delete ns data web cert-manager monitoring apps edge

# Final proof (should return nothing)
$ helm list -A
```

---

## **How to think & work quickly with Helm under exam pressure**

1. **Always start with helm search repo <chart>**
    
    It shows available versions and confirms you added/updated the repo correctly.
    
2. **helm show values <chart> | less** gives the full default values in one command.
    
    Combine with / search inside _less_ to locate the field to override.
    
3. **Prefer --set for ≤ 3 simple overrides**; otherwise create a small file:
    

```
cat <<EOF > override.yaml
replicaCount: 3
service:
  type: NodePort
  nodePorts:
    http: 30080
EOF

helm install ... -f override.yaml
```

3.   
    
4. **Use helm get values <release> after install/upgrade** to confirm what actually went into the release.
    
5. **Revision workflow staples**
    
    - helm history <release> – know the revision numbers.
        
    - helm rollback <release> <rev> – fast rollback.
        
    - helm diff upgrade (plugin) is not installed in exam clusters, so rely on helm template for diffs.
        
    
6. **CRD guardrails**
    
    - If the chart has CRDs **and** you need them → installCRDs=true **or** run the bundled kubectl apply -f crds/.
        
    - If CRDs already exist and you must avoid touching them → --skip-crds.
        
    
7. **Packaging / local path install**
    
    - helm create → modify values → helm package → helm install ./mychart-x.y.z.tgz.
        
    - Local path avoids relying on remote repos (no internet).
        
    
8. **Namespace hygiene**
    
    The grader tests both resources **and** namespace names. Add --namespace … --create-namespace on **every** install.
    
9. **Speed loops** for CRD readiness
    

```
until kubectl get crd <name> ; do sleep 2; done
```

9.   
    
10. **One window, two panes**
    
    Split your terminal (tmux, screen, VS Code) so the left pane is commands, right pane continuously tails:
    

```
watch -n2 'helm list -A; echo; kubectl get pods -A'
```

  

---

### **Extra practice idea: Argo CD scenario (like your previous attempt)**

1. Add repo, install **argocd/argo-cd** chart at a specified version with custom configs.cm.application.instanceLabelKey override.
    
2. Expose its server service as NodePort – override server.service.type and nodePort.
    
3. Create a sample Application CR in the same namespace pointing to a public Git repo.
    

  

Try solving it end-to-end with the techniques above: search → show values → choose overrides → install → verify CR and pods.

---

### **Final encouragement**

  

Mastering Helm for CKA is mostly **pattern recognition** and **muscle memory**. Re-type these tasks until you can finish them in **≤ 25 minutes total** without looking anything up. Good luck—you’ve got this! 🚀