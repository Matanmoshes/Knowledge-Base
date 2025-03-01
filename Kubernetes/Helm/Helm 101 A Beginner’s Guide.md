## 1. What Is Helm?

Helm is a **package manager** for Kubernetes. Instead of manually creating or applying multiple YAML files, you can use Helm _charts_ (packaged sets of YAML templates) to define, install, upgrade, and manage your Kubernetes applications in a more organized, versioned, and reusable way.

**Key Terms**:

- **Chart**: A Helm package that contains the metadata (`Chart.yaml`), default configuration (`values.yaml`), and templates (`templates/*.yaml`) to deploy a Kubernetes application.
- **Release**: A specific instance of a Helm chart deployed to your cluster. You can install the same chart multiple times with different release names.
- **Repository**: A place where charts are stored and shared (e.g., `https://charts.bitnami.com/bitnami`).

---

## 2. Installing Helm

Installation methods vary based on your OS, but the most common approach:

### Mac (Homebrew)

```bash
brew install helm
```

### Linux

Download the latest release from the Helm GitHub and install:

```bash
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
```

### Windows

Download the binary from the releases page or install with [Chocolatey](https://chocolatey.org/packages/kubernetes-helm).

**Verify**:

```bash
helm version
```

You should see something like `version.BuildInfo{Version:"v3.x.x", ...}`

---

## 3. Basic Helm Workflow

### 3.1 Adding a Chart Repository (Optional)

If you want to install charts from public repositories (like Bitnami or the official stable repo), you need to add the repo first, for example:

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
```

You can then search for charts:

```bash
helm search repo bitnami
```

### 3.2 Installing a Chart

When you install a chart, you specify:

1. The **chart name** (and optional version).
2. The **release name** (what you want the installation to be called).

Example installing Bitnami’s WordPress chart:

```bash
helm install my-wordpress bitnami/wordpress
```

- **`my-wordpress`** = release name
- **`bitnami/wordpress`** = chart name from the `bitnami` repo

When done, Helm will create or update resources in your Kubernetes cluster (pods, services, etc.).

### 3.3 Viewing Installed Releases

```bash
helm list
```

Shows all releases in the current namespace. You can see the status (e.g., `deployed`, `failed`), the revision, and when it was last updated.

Use `-A` to show releases in all namespaces:

```bash
helm list -A
```

### 3.4 Checking Release Status

```bash
helm status my-wordpress
```

Displays which resources were created, relevant events, and any notes/instructions (like how to access the service).

### 3.5 Uninstalling a Release

```bash
helm uninstall my-wordpress
```

This removes all Kubernetes resources associated with that release.

---

## 4. Creating and Working with Your Own Charts

### 4.1 Starting a New Chart

Helm can scaffold a basic chart directory:

```bash
helm create my-chart
```

It creates a structure like:

```
my-chart/
  Chart.yaml
  values.yaml
  charts/
  templates/
    ...
```

- **Chart.yaml** – Contains chart metadata (name, version, etc.).
- **values.yaml** – Default configuration values (image tags, replicas, etc.).
- **templates** – YAML templates for resources (Deployment, Service, etc.), which can reference the values from `values.yaml`.

### 4.2 Template Basics

Inside `templates/`, you’ll see files like `deployment.yaml`, `service.yaml`, etc. These contain standard Kubernetes YAML with **Go template** placeholders, for example:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "my-chart.fullname" . }}
spec:
  replicas: {{ .Values.replicaCount }}
  template:
    spec:
      containers:
        - name: my-app
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
```

`{{ .Values.xyz }}` will be replaced by data from `values.yaml` at install time.

### 4.3 Linting (Validating) a Chart

To check if your chart is well-formed:

```bash
helm lint ./my-chart
```

If you see errors, fix them before installing.

### 4.4 Installing Your Chart

You can install your local chart by pointing helm to its directory:

```bash
helm install my-release ./my-chart
```

- **`my-release`** is your chosen release name.
- **`./my-chart`** is the path to your chart.

### 4.5 Overriding Values

If you want to change the default values from `values.yaml` at install time, use `--set` flags or provide a separate `values` file:

```bash
helm install my-release ./my-chart \
  --set replicaCount=3 \
  --set image.tag="2.0"
```

Or:

```bash
helm install my-release ./my-chart -f custom-values.yaml
```

---

## 5. Upgrading and Rolling Back

### 5.1 Upgrading

When you have a new chart version or want to change something (like scaling), you can run:

```bash
helm upgrade my-release ./my-chart
```

Helm will apply only the changes needed for the new configuration.

If you’re using a remote chart from a repo, it looks like:

```bash
helm upgrade my-release bitnami/wordpress --version 10.0.0
```

### 5.2 Rollback

If something goes wrong, you can roll back to a previous revision:

```bash
helm rollback my-release 1
```

You can see revisions with:

```bash
helm history my-release
```

Which shows a table of previous deployments (install, upgrades, etc.) along with revision numbers.

---

## 6. Practical Example

1. **Create a Chart**:
    
    ```bash
    helm create my-chart
    ```
    
2. **Inspect and Lint**:
    
    ```bash
    cd my-chart
    helm lint .
    ```
    
3. **Install** (in default namespace):
    
    ```bash
    helm install my-app .
    ```
    
4. **Check**:
    
    ```bash
    helm list
    helm status my-app
    ```
    
5. **Upgrade** (changing replicaCount to 3):
    
    ```bash
    helm upgrade my-app . --set replicaCount=3
    ```
    
6. **Rollback** if needed:
    
    ```bash
    helm rollback my-app 1
    ```
    
7. **Uninstall**:
    
    ```bash
    helm uninstall my-app
    ```
    

---

## 7. Common Helm Commands Reference

8. **helm repo add** `<name> <url>`  
    Add a chart repository.
    
9. **helm search repo** `<keyword>`  
    Search for charts in the added repositories.
    
10. **helm install** `<release-name> <chart-path-or-repo>`  
    Install a chart to create a new release.
    
11. **helm list**  
    List releases in the current namespace (add `-A` for all namespaces).
    
12. **helm status** `<release-name>`  
    Show the status of a release.
    
13. **helm upgrade** `<release-name> <chart-path-or-repo>`  
    Upgrade an existing release.
    
14. **helm rollback** `<release-name> <revision>`  
    Roll back to a previous release revision.
    
15. **helm uninstall** `<release-name>`  
    Uninstall (remove) a release.
    
16. **helm package** `<chart-path>`  
    Package the chart into a `.tgz` file for sharing.
    
17. **helm lint** `<chart-path>`  
    Check chart syntax and structure.
    

---
# Conclusion

Using Helm streamlines your Kubernetes application lifecycle—from creation to installation, upgrades, rollbacks, and uninstalls. This guide should help you get hands‐on experience quickly, while also providing a solid reference for day‐to‐day Helm usage. Practice with simple applications first, explore the templating features, and then scale your knowledge to multi-environment, multi‐chart scenarios. Good luck with your Helm journey!