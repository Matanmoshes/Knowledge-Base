# Guide to RBAC in Kubernetes 

Role-Based Access Control (RBAC) is a critical security feature in Kubernetes that allows you to regulate access to resources within your cluster. By defining roles and assigning them to users or groups, you can control who can perform specific actions on various resources.

This guide will walk you through the concepts of RBAC in Kubernetes and provide practical examples of creating **Roles**, **RoleBindings**, **ClusterRoles**, and **ClusterRoleBindings**.

---

## Table of Contents

1. [Understanding RBAC Components](#understanding-rbac-components)
    - [Role](#role)
    - [ClusterRole](#clusterrole)
    - [RoleBinding](#rolebinding)
    - [ClusterRoleBinding](#clusterrolebinding)
2. [When to Use Roles vs. ClusterRoles](#when-to-use-roles-vs-clusterroles)
3. [Creating a Namespace for Testing](#creating-a-namespace-for-testing)
4. [Example: Creating a Role](#example-creating-a-role)
5. [Example: Creating a RoleBinding](#example-creating-a-rolebinding)
6. [Example: Creating a ClusterRole](#example-creating-a-clusterrole)
7. [Example: Creating a ClusterRoleBinding](#example-creating-a-clusterrolebinding)
8. [Verifying Access](#verifying-access)
9. [Cleaning Up](#cleaning-up)
10. [Conclusion](#conclusion)

---

## Understanding RBAC Components

### Role

- **Definition**: A Role defines a set of permissions within a specific namespace.
- **Scope**: Namespace-scoped.
- **Use Case**: Granting permissions to resources like Pods, Deployments, or Services within a namespace.

### ClusterRole

- **Definition**: Similar to a Role but applicable across the entire cluster.
- **Scope**: Cluster-wide.
- **Use Case**: Granting permissions to cluster-scoped resources like Nodes or PersistentVolumes, or granting namespace-scoped permissions across all namespaces.

### RoleBinding

- **Definition**: Associates a Role with a user or a set of users within a namespace.
- **Scope**: Namespace-scoped.
- **Use Case**: Assigning a Role to a user in a specific namespace.

### ClusterRoleBinding

- **Definition**: Associates a ClusterRole with a user or a set of users across the entire cluster.
- **Scope**: Cluster-wide.
- **Use Case**: Assigning cluster-wide permissions to a user or group.

---

## When to Use Roles vs. ClusterRoles

- **Roles and RoleBindings**: Use when you want to grant permissions within a specific namespace.
- **ClusterRoles and ClusterRoleBindings**: Use when you need to grant permissions cluster-wide or to cluster-scoped resources.

---

## Creating a Namespace for Testing

Let's create a separate namespace to keep our resources organized.

```bash
kubectl create namespace rbac-demo
```

---

## Example: Creating a Role

We will create a Role that allows read access to Pods within the `rbac-demo` namespace.

### YAML Definition: `pod-reader-role.yaml`

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: rbac-demo
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
```

### Apply the Role

```bash
kubectl apply -f pod-reader-role.yaml
```

---

## Example: Creating a RoleBinding

We will bind the `pod-reader` Role to a user named `jane`.

### YAML Definition: `pod-reader-rolebinding.yaml`

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods
  namespace: rbac-demo
subjects:
- kind: User
  name: jane
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

### Apply the RoleBinding

```bash
kubectl apply -f pod-reader-rolebinding.yaml
```

---

## Example: Creating a ClusterRole

We will create a ClusterRole that allows listing of all namespaces.

### YAML Definition: `namespace-reader-clusterrole.yaml`

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: namespace-reader
rules:
- apiGroups: [""]
  resources: ["namespaces"]
  verbs: ["get", "list", "watch"]
```

### Apply the ClusterRole

```bash
kubectl apply -f namespace-reader-clusterrole.yaml
```

---

## Example: Creating a ClusterRoleBinding

We will bind the `namespace-reader` ClusterRole to a user named `john`.

### YAML Definition: `namespace-reader-clusterrolebinding.yaml`

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: read-namespaces
subjects:
- kind: User
  name: john
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: namespace-reader
  apiGroup: rbac.authorization.k8s.io
```

### Apply the ClusterRoleBinding

```bash
kubectl apply -f namespace-reader-clusterrolebinding.yaml
```

---

## Verifying Access

### Simulating User Access

Since Kubernetes does not have built-in users, we can simulate user access using `kubectl` with the `--as` flag.

#### Verify `jane` Can Read Pods in `rbac-demo` Namespace

```bash
kubectl --as=jane -n rbac-demo get pods
```

- **Expected Result**: Should list Pods in `rbac-demo` namespace (even if none are present).

#### Verify `jane` Cannot Read Pods in `default` Namespace

```bash
kubectl --as=jane -n default get pods
```

- **Expected Result**: Should receive a "forbidden" error.

#### Verify `john` Can List All Namespaces

```bash
kubectl --as=john get namespaces
```

- **Expected Result**: Should list all namespaces in the cluster.

#### Verify `john` Cannot Get Pods

```bash
kubectl --as=john -n rbac-demo get pods
```

- **Expected Result**: Should receive a "forbidden" error.

---

## Cleaning Up

To remove the resources created during this guide, run:

```bash
kubectl delete role pod-reader -n rbac-demo
kubectl delete rolebinding read-pods -n rbac-demo
kubectl delete clusterrole namespace-reader
kubectl delete clusterrolebinding read-namespaces
kubectl delete namespace rbac-demo
```


---

**Additional Resources:**

- [Kubernetes RBAC Documentation](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)


