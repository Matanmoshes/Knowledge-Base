# Kubernetes Manifest Files and Key Concepts

In this summary, I will cover essential Kubernetes components, including manifest files, Deployments, ReplicaSets, Pods, Services, NodePort, ClusterIP, LoadBalancer, and Ingress.

---
## Manifest Files

A **manifest file** in Kubernetes is a YAML or JSON file that declaratively defines the desired state of a Kubernetes object. By using manifest files, I can version control my configurations and apply them consistently across environments.

Example of a Pod manifest file:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
    - name: my-container
      image: nginx
```

---

## Pods

A **Pod** is the smallest deployable unit in Kubernetes and represents a single instance of a running process in the cluster. Each pod can contain one or more containers that share the same network namespace and storage volumes.

---
## ReplicaSets

A **ReplicaSet** ensures that a specified number of pod replicas are running at any given time. It replaces pods that are deleted or fail and is mainly used by Deployments to maintain the desired number of pods.

Example of a ReplicaSet manifest:

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: my-replicaset
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: my-container
          image: nginx
```


---

## Deployments

A **Deployment** provides declarative updates for Pods and ReplicaSets. It allows me to roll out updates, roll back to previous versions, and scale the number of replicas seamlessly.

Example of a Deployment manifest:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: my-container
          image: nginx
```


Example of Deployment manifest with service NodePort:

```YAML
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: my-container
          image: nginx
---
apiVersion: v1
kind: Service
metadata:
  name: my-nodeport-service
spec:
  type: NodePort
  selector:
    app: my-app
  ports:
    - port: 80        # Service port
      targetPort: 80  # Container port
      nodePort: 30007 # External port on the node
```


---

## Services

A **Service** is an abstraction that defines a logical set of Pods and a policy to access them. Services enable loose coupling between dependent Pods.

---
### ClusterIP

The **ClusterIP** service is the default type, exposing the service on an internal IP within the cluster. This makes the service only reachable from within the cluster.

Example:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-clusterip-service
spec:
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```

---

### NodePort

A **NodePort** service exposes the service on each node's IP at a static port (the NodePort). It's accessible externally using `<NodeIP>:<NodePort>`.

Example:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-nodeport-service
spec:
  type: NodePort
  selector:
    app: my-app
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30007
```


---

### LoadBalancer

A **LoadBalancer** service exposes the service externally using a cloud provider's load balancer. It automatically provisions a load balancer and assigns a public IP.

Example:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-loadbalancer-service
spec:
  type: LoadBalancer
  selector:
    app: my-app
  ports:
    - port: 80
      targetPort: 80
```


---

## Ingress

An **Ingress** is an API object that manages external access to services in a cluster, typically HTTP. It provides load balancing, SSL termination, and name-based virtual hosting.

Example of an Ingress manifest:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-ingress
spec:
  rules:
    - host: myapp.example.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: my-service
                port:
                  number: 80
```


---

## Conclusion

By understanding these Kubernetes components and how to define them using manifest files, I can effectively deploy, scale, and manage applications:

- **Pods**: The basic unit of deployment.
- **ReplicaSets**: Maintain a stable set of replica Pods.
- **Deployments**: Manage ReplicaSets and provide declarative updates.
- **Services**: Expose Pods internally or externally.
  - **ClusterIP**: Internal cluster communication.
  - **NodePort**: External access via node IP and port.
  - **LoadBalancer**: External access via cloud provider's load balancer.
- **Ingress**: Manage external HTTP/HTTPS access to services.

Using these resources, I can build robust and scalable applications on Kubernetes.