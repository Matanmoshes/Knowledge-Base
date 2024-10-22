## Introduction

While Kubernetes inherently supports high availability for containerized applications, it's equally important to ensure that the **Kubernetes cluster itself** is highly available. This involves setting up **multiple control plane nodes** to eliminate single points of failure.

---
## Multiple Control Plane Nodes

Having multiple instances of the control plane components—such as the **kube-apiserver**—distributed across different nodes enhances the cluster's resilience.

- **Load Balancer**: Communication with the Kubernetes API server is managed through a load balancer, which directs requests from tools like `kubectl` and the kubelets on worker nodes to the available control plane nodes.
- **Worker Nodes Communication**: The worker nodes also interact with the API server via the load balancer, ensuring consistent and reliable connectivity.

![[Pasted image 20241018184557.png]]

---

## Managing etcd for High Availability

A key aspect of high availability is the management of **etcd**, the distributed key-value store used by Kubernetes. There are two main design patterns for this:

### 1. Stacked etcd

In this configuration:

- **Same Nodes**: etcd runs on the same nodes as the control plane components.
- **Individual Instances**: Each control plane node hosts its own etcd instance.
- **Usage**: This is the approach used by **kubeadm**, which I've been utilizing throughout my work.

![[Pasted image 20241018184634.png]]

### 2. External etcd

In this setup:

- **Separate Nodes**: etcd runs on nodes dedicated solely to etcd.
- **Isolation**: This allows for greater scalability and isolates etcd from the control plane nodes.
- **Independent Cluster**: Multiple external etcd nodes form their own cluster, independent of the control plane nodes.

![[Pasted image 20241018184701.png]]

---

## Reference: 
- https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/ha-topology/