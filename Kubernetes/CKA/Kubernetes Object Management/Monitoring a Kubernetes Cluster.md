# Guide to Monitoring a Kubernetes Cluster with Metrics Server

**Metrics Server** is a scalable, efficient source of container resource metrics for Kubernetes built-in autoscaling pipelines. It collects resource usage data (CPU and memory) from the Kubelets and exposes them through the Kubernetes API Server. This guide will walk you through installing and configuring the Metrics Server in your Kubernetes cluster using the repository provided.

---

## Table of Contents

1. [Introduction to Metrics Server](#introduction-to-metrics-server)
2. [Prerequisites](#prerequisites)
3. [Cloning the Metrics Server Repository](#cloning-the-metrics-server-repository)
4. [Deploying Metrics Server](#deploying-metrics-server)
5. [Verifying Metrics Server Deployment](#verifying-metrics-server-deployment)
6. [Using Metrics Server](#using-metrics-server)
7. [Troubleshooting](#troubleshooting)
8. [Conclusion](#conclusion)
9. [Additional Resources](#additional-resources)

---

## Introduction to Metrics Server

- **Metrics Server** is a cluster-wide aggregator of resource usage data.
- It collects metrics from the Summary API, exposed by Kubelet on each node.
- Metrics Server is essential for the Kubernetes **Horizontal Pod Autoscaler** and **Vertical Pod Autoscaler** to function.
- It replaces the deprecated **Heapster** component.

---

## Prerequisites

Before you begin, ensure you have the following:

- A running Kubernetes cluster (version 1.8 or newer).
- `kubectl` configured to interact with your cluster.
- Basic understanding of Kubernetes concepts.

---

## Cloning the Metrics Server Repository

The `kodekloudhub/kubernetes-metrics-server` repository contains the necessary YAML manifests to deploy Metrics Server.

### Steps:

1. **Clone the Repository**

   Open your terminal and run:

   ```bash
   git clone https://github.com/kodekloudhub/kubernetes-metrics-server.git
   ```

2. **Navigate to the Directory**

   ```bash
   cd kubernetes-metrics-server
   ```

---

## Deploying Metrics Server

### Steps:

1. **Deploy Metrics Server**

   Apply the YAML manifests provided in the repository:

   ```bash
   kubectl apply -f .
   ```

   This command applies all the YAML files in the current directory.

2. **Verify Deployment**

   Check that the Metrics Server components are deployed:

   ```bash
   kubectl get deployments -n kube-system
   ```

   You should see a deployment named `metrics-server`.

3. **Check the Pods**

   ```bash
   kubectl get pods -n kube-system
   ```

   Ensure the `metrics-server` pod is running.

---

## Verifying Metrics Server Deployment

After deploying, it's important to verify that Metrics Server is functioning correctly.

### Steps:

1. **Check API Resources**

   Verify that the `metrics.k8s.io` API is available:

   ```bash
   kubectl api-versions | grep metrics
   ```

   Expected output:

   ```
   metrics.k8s.io/v1beta1
   ```

2. **Use `kubectl top` Commands**

   - **View Node Metrics**

     ```bash
     kubectl top nodes
     ```

     Example output:

     ```
     NAME           CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%
     node1          250m         13%    1024Mi          50%
     node2          200m         10%    800Mi           40%
     ```

   - **View Pod Metrics**

     ```bash
     kubectl top pods --all-namespaces
     ```

     Example output:

     ```
     NAMESPACE     NAME                            CPU(cores)   MEMORY(bytes)
     default       nginx-deployment-5c689d7c4f-abcde   10m          20Mi
     kube-system   coredns-6955765f44-abcde           5m           10Mi
     ```

---

## Using Metrics Server

Metrics Server enables you to monitor resource usage and is a prerequisite for autoscaling.

### Horizontal Pod Autoscaler (HPA)

- **Example**: Create an HPA that scales a deployment based on CPU utilization.

  ```bash
  kubectl autoscale deployment nginx-deployment --cpu-percent=50 --min=1 --max=10
  ```

- **Check HPA Status**

  ```bash
  kubectl get hpa
  ```

  Output:

  ```
  NAME               REFERENCE                     TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
  nginx-deployment   Deployment/nginx-deployment   10%/50%   1         10        1          5m
  ```

---

## Troubleshooting

If you encounter issues, consider the following troubleshooting steps.

### Common Issues and Solutions

1. **Metrics Not Available**

   - **Symptom**: `kubectl top` commands return errors like `Error from server (ServiceUnavailable): the server is currently unable to handle the request`.
   - **Solution**:
     - Check the Metrics Server logs:

       ```bash
       kubectl logs -n kube-system deploy/metrics-server
       ```

     - Look for errors related to certificate validation or connectivity.

2. **Certificate Issues**

   - Metrics Server may have issues verifying node certificates, especially in self-hosted clusters.
   - **Solution**:
     - Modify the Metrics Server deployment to include the following arguments:

       ```yaml
       command:
       - /metrics-server
       - --kubelet-insecure-tls
       - --kubelet-preferred-address-types=InternalIP
       ```

     - **Note**: Adding `--kubelet-insecure-tls` bypasses TLS certificate verification with the Kubelet. Use with caution and only in secure, trusted environments.

     - Update the deployment:

       ```bash
       kubectl edit deploy -n kube-system metrics-server
       ```

       Modify the `command` section accordingly.

3. **Firewall and Network Policies**

   - Ensure there are no network policies or firewall rules blocking communication between Metrics Server and Kubelets.

4. **Version Compatibility**

   - Metrics Server requires Kubernetes 1.8 or newer.
   - Ensure your cluster components are up to date.

---

## Conclusion

You have successfully set up Metrics Server in your Kubernetes cluster. With Metrics Server running, you can now monitor resource usage and implement autoscaling based on real-time metrics.

**Key Takeaways**:

- Metrics Server is essential for resource monitoring and autoscaling.
- Deploying Metrics Server involves applying the provided YAML manifests.
- Verify the deployment using `kubectl top` commands.
- Troubleshoot common issues by checking logs and ensuring correct configurations.

---

## Additional Resources

- [Metrics Server GitHub Repository](https://github.com/kubernetes-sigs/metrics-server)
- [Kubernetes Documentation: Metrics Server](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_top/kubectl_top_pod/)

