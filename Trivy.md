# trivy on kubernetes

To scan a pod or node in your Kubernetes cluster with Trivy, you need to leverage the **Trivy Operator** (an integration with Kubernetes), which can scan the cluster's resources directly. Here's how to proceed:

### 1. **Setup Trivy Operator:**
   - Apply the official deployment manifest to install Trivy Operator in your Kubernetes cluster:
   ```bash
   kubectl apply -f https://raw.githubusercontent.com/aquasecurity/trivy-operator/main/deploy/static/trivy-operator.yaml
   ```

   - Verify that the `trivy-operator` pod is running:
   ```bash
   kubectl get pods -n trivy-system
   ```

### 2. **Scan Pods/Nodes:**
   Trivy Operator will automatically create reports for your cluster's workloads (pods and nodes). You can retrieve these reports via `kubectl`.

   - **List Available Reports:**
     - **Pods (VulnerabilityReports):**
     ```bash
     kubectl get vulnerabilityreports -A
     ```

     - **Nodes (ClusterComplianceReports):**
     ```bash
     kubectl get clustercompliancereports -A
     ```

   - **Describe Specific Reports:**
     To inspect a particular pod or node report:
     - **Pod Report:**
     ```bash
     kubectl describe vulnerabilityreports <report-name> -n <namespace>
     ```

     Replace `<report-name>` with the name of the specific pod's vulnerability report.

     - **Node Report:**
     ```bash
     kubectl describe clustercompliancereports <report-name>
     ```

### 3. **Check Operator Logs:**
   If you encounter any issues or errors with Trivy Operator, review the logs:
   ```bash
   kubectl logs -n trivy-system <trivy-operator-pod-name>
   ```

   Replace `<trivy-operator-pod-name>` with the actual pod name, which can be obtained via:
   ```bash
   kubectl get pods -n trivy-system
   ```

---

### View in table-like structure.


1. **Basic Table Format:**
   `kubectl get vulnerabilityreports -A -o wide`

   This gives a wide table format across all namespaces with extra information.

2. **Custom Column Output:**
   You can customize the columns using `kubectl`'s `-o custom-columns` flag. For instance:

   ```bash
   kubectl get vulnerabilityreports -A -o custom-columns="NAMESPACE:.metadata.namespace,NAME:.metadata.name,REPOSITORY:.spec.imageRef,SCANNER:.spec.scanner.name,CRITICAL:.report.summary.criticalCount,HIGH:.report.summary.highCount,MEDIUM:.report.summary.mediumCount,LOW:.report.summary.lowCount"
   ```

   - `NAMESPACE`: The namespace where the vulnerability report is found.
   - `NAME`: The name of the vulnerability report.
   - `REPOSITORY`: The image repository reference.
   - `SCANNER`: The name of the scanner used (Trivy).
   - `CRITICAL`, `HIGH`, `MEDIUM`, and `LOW`: Counts of vulnerabilities of different severity levels.

### Customize Output Further
Adjust the columns and their respective fields based on what you need to see. You can reference the full field paths by inspecting the detailed JSON output with:

```bash
kubectl get vulnerabilityreports -A -o json
```

This will help you understand which fields you might want to include.