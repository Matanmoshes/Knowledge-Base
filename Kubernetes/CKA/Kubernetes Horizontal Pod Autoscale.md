# Kubernetes Horizontal Pod Autoscaler (HPA) Tutorial

This documentation guides you through setting up a Kubernetes Horizontal Pod Autoscaler (HPA) for a sample application, including creating the necessary deployments and services.

## Table of Contents

1. [Introduction](#introduction)
2. [Prerequisites](#prerequisites)
3. [Creating the Deployment](#creating-the-deployment)
4. [Creating the Service](#creating-the-service)
   - [ClusterIP Service](#clusterip-service)
   - [NodePort Service](#nodeport-service)
5. [Setting Up the Horizontal Pod Autoscaler](#setting-up-the-horizontal-pod-autoscaler)
6. [Accessing the Application](#accessing-the-application)
7. [Testing the HPA](#testing-the-hpa)
8. [Conclusion](#conclusion)

---

## Introduction

The Kubernetes Horizontal Pod Autoscaler (HPA) automatically scales the number of pods in a deployment or replica set based on observed CPU utilization or other select metrics. This tutorial demonstrates how to configure an HPA for a sample application, ensuring that your application scales up or down according to demand.

---

## Prerequisites

- A running Kubernetes cluster (version 1.18 or later recommended).
- `kubectl` command-line tool configured to communicate with your cluster.
- Metrics Server installed in the cluster (required for HPA to retrieve metrics).

---

## Creating the Deployment

First, we'll create a deployment for the sample application using the image `gcr.io/google_containers/hpa-example`, which is designed for testing HPA functionality.

**Deployment YAML (`deployment.yml`):**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hpa-example
  labels:
    app: hpa-example
spec:
  replicas: 3
  selector:
    matchLabels:
      app: hpa-example
  template:
    metadata:
      labels:
        app: hpa-example
    spec:
      containers:
      - name: hpa-example
        image: gcr.io/google_containers/hpa-example
        ports:
        - containerPort: 80
        resources:
          requests:
            memory: "250Mi"
            cpu: "500m"
```

**Explanation:**

- **apiVersion**: Specifies the API version (`apps/v1`).
- **kind**: Defines the resource type (`Deployment`).
- **metadata**: Contains metadata like the name and labels.
- **spec**:
  - **replicas**: The initial number of pod replicas (set to 3).
  - **selector**: Defines how the Deployment finds which pods to manage.
  - **template**:
    - **metadata**: Labels for the pods.
    - **spec**:
      - **containers**: Defines the container settings.
        - **image**: The container image to use.
        - **ports**: Container ports to expose.
        - **resources**: Resource requests and limits for the container.

**Apply the Deployment:**

```bash
kubectl apply -f deployment.yml
```

---

## Creating the Service

To allow network access to your application, create a Service that exposes the Deployment's pods.

### ClusterIP Service

By default, a `ClusterIP` service is only accessible within the cluster.

**Service YAML (`service-clusterip.yml`):**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: hpa-example-service
  labels:
    app: hpa-example
spec:
  selector:
    app: hpa-example
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: ClusterIP
```

**Explanation:**

- **apiVersion**: The API version for a Service is `v1`.
- **kind**: Specifies that the resource is a `Service`.
- **metadata**: Contains the name and labels.
- **spec**:
  - **selector**: Matches the pods with label `app: hpa-example`.
  - **ports**: Defines the ports that the service will expose.
  - **type**: `ClusterIP` makes the service accessible only within the cluster.

**Apply the Service:**

```bash
kubectl apply -f service-clusterip.yml
```

### NodePort Service

To access the application from outside the cluster (e.g., from your local machine), create a `NodePort` service.

**Service YAML (`service-nodeport.yml`):**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: hpa-example-nodeport
  labels:
    app: hpa-example
spec:
  selector:
    app: hpa-example
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 30080
  type: NodePort
```

**Explanation:**

- **type**: Set to `NodePort` to expose the service externally.
- **nodePort**: Specifies the port on each node to expose (must be between 30000-32767).

**Apply the Service:**

```bash
kubectl apply -f service-nodeport.yml
```

---

## Setting Up the Horizontal Pod Autoscaler

Now, configure the HPA to automatically scale the deployment based on CPU utilization.

**HPA YAML (`autoscaler.yml`):**

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: hpa-example-autoscaler
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: hpa-example
  minReplicas: 5
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 50
```

**Explanation:**

- **apiVersion**: Uses `autoscaling/v2` for advanced metric support.
- **kind**: `HorizontalPodAutoscaler`.
- **metadata**: Contains the name.
- **spec**:
  - **scaleTargetRef**: Specifies the target Deployment to scale.
  - **minReplicas**: Minimum number of pods.
  - **maxReplicas**: Maximum number of pods.
  - **metrics**: Defines the metric to monitor (CPU utilization at 50%).

- **`kind: Deployment`**: This tells the HPA that it should target a Kubernetes **Deployment** resource.
- **`name: hpa-example`**: This is the name of the specific Deployment that the HPA will manage.
- **`apiVersion: apps/v1`**: This specifies the API version of the Deployment resource.


**Apply the HPA:**

```bash
kubectl apply -f autoscaler.yml
```

**Verify the HPA:**

```bash
kubectl get hpa
```

**Sample Output:**

```
NAME                     REFERENCE                TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
hpa-example-autoscaler   Deployment/hpa-example   10%/50%   5         10        5          1m
```

---

## Accessing the Application

To test the application and the HPA, you need to access the application endpoints.

**Get Node IPs:**

```bash
kubectl get nodes -o wide
```

**Sample Output:**

```
NAME     STATUS   ROLES    AGE   VERSION   INTERNAL-IP      EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION   CONTAINER-RUNTIME
node01   Ready    <none>   1d    v1.21.1   192.168.1.10     <none>        Ubuntu 20.04 LTS     5.4.0-72-generic   docker://20.10.6
node02   Ready    <none>   1d    v1.21.1   192.168.1.11     <none>        Ubuntu 20.04 LTS     5.4.0-72-generic   docker://20.10.6
```

**Access the Application Using `curl` or `wget`:**

```bash
curl http://192.168.1.10:30080
```

**Note:** Replace `192.168.1.10` with the IP address of one of your nodes.

---

## Testing the HPA

To see the HPA in action, you can simulate CPU load on the application pods.

**Generate Load:**

Run a CPU-intensive task inside the pods to increase CPU utilization.

**Option 1: Using `kubectl exec`**

```bash
kubectl get pods -l app=hpa-example
```

**Sample Output:**

```
NAME                           READY   STATUS    RESTARTS   AGE
hpa-example-56597bcdd5-7r5j9   1/1     Running   0          20m
hpa-example-56597bcdd5-8ccck   1/1     Running   0          20m
...
```

Pick one of the pod names and run:

```bash
kubectl exec -it hpa-example-56597bcdd5-7r5j9 -- /bin/sh
```

Inside the pod, run:

```sh
while true; do :; done
```

This command will create a busy loop, consuming CPU cycles.

**Option 2: Using Load Testing Tools**

You can use a tool like `ab` (ApacheBench) or `hey` to send HTTP requests to your application, increasing CPU usage.

**Example using `ab`:**

```bash
ab -n 100000 -c 100 http://192.168.1.10:30080/
```

- `-n 100000`: Total number of requests to perform.
- `-c 100`: Number of multiple requests to make at a time.

**Monitor the HPA:**

Watch the HPA to see if it scales the number of pods.

```bash
kubectl get hpa hpa-example-autoscaler --watch
```

**Sample Output:**

```
NAME                     REFERENCE                TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
hpa-example-autoscaler   Deployment/hpa-example   70%/50%    5         10        5          25m
hpa-example-autoscaler   Deployment/hpa-example   85%/50%    5         10        7          27m
```

As the CPU utilization exceeds the target of 50%, the HPA increases the number of replicas to handle the load.

---

## Conclusion

You've successfully set up a Kubernetes Horizontal Pod Autoscaler for your application. By monitoring CPU utilization, the HPA automatically adjusts the number of pods to maintain optimal performance. This ensures that your application can handle varying loads efficiently without manual intervention.

---

## Additional Notes

- **Metrics Server:** Ensure the Metrics Server is running in your cluster. Without it, the HPA cannot retrieve the necessary metrics.

- **Checking Metrics Server:**

  ```bash
  kubectl top nodes
  kubectl top pods
  ```

  If these commands return metrics, your Metrics Server is operational.

- **Cleaning Up:**

  To remove the resources created in this tutorial:

  ```bash
  kubectl delete -f autoscaler.yml
  kubectl delete -f service-nodeport.yml
  kubectl delete -f deployment.yml
  ```


