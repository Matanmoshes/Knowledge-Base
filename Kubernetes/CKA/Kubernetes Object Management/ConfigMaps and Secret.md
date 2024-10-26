# Managing Application Configuration on Kubernetes

In this guide, we'll explore how to manage application configuration in Kubernetes using ConfigMaps and Secrets. We'll cover how to create them, and how to pass configuration data to your containers using environment variables and volumes.

## Table of Contents

1. [Introduction](#introduction)
2. [ConfigMaps](#configmaps)
   - [Creating a ConfigMap](#creating-a-configmap)
3. [Secrets](#secrets)
   - [Creating a Secret](#creating-a-secret)
4. [Passing Configuration Data to Pods](#passing-configuration-data-to-pods)
   - [Using Environment Variables](#using-environment-variables)
   - [Using Volumes](#using-volumes)
5. [Hands-On Demonstration](#hands-on-demonstration)
   - [Using ConfigMaps with Environment Variables](#using-configmaps-with-environment-variables)
   - [Using Secrets with Volumes](#using-secrets-with-volumes)
6. [Conclusion](#conclusion)

---

## Introduction

Application configuration involves supplying dynamic values to your applications at runtime to control their behavior. In Kubernetes, this is typically managed using **ConfigMaps** and **Secrets**, which allow you to decouple configuration artifacts from image content, making your applications more portable.

## ConfigMaps

### What is a ConfigMap?

A **ConfigMap** is an API object used to store non-confidential data in key-value pairs. Pods can consume ConfigMaps as environment variables, command-line arguments, or as configuration files in a volume.

### Creating a ConfigMap

You can create a ConfigMap from a YAML file or directly using the `kubectl` command.

**Example YAML file (`configmap.yaml`):**

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-configmap
data:
  app.name: "MyApp"
  app.message: "Welcome to MyApp!"
```

Create the ConfigMap:

```bash
kubectl apply -f configmap.yaml
```

Alternatively, create a ConfigMap directly from literal values:

```bash
kubectl create configmap my-configmap \
  --from-literal=app.name="MyApp" \
  --from-literal=app.message="Welcome to MyApp!"
```

## Secrets

### What is a Secret?

A **Secret** is similar to a ConfigMap but is intended to hold sensitive information like passwords, tokens, or keys. Secret data is stored in base64-encoded form.

### Creating a Secret

You can create a Secret from a YAML file or using the `kubectl` command.

**Example YAML file (`secret.yaml`):**

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-secret
type: Opaque
data:
  username: bXktdXNlcm5hbWU=
  password: bXktcGFzc3dvcmQ=
```

*Note: The values for `username` and `password` are base64-encoded.*

Create the Secret:

```bash
kubectl apply -f secret.yaml
```

Alternatively, create a Secret directly from literal values:

```bash
kubectl create secret generic my-secret \
  --from-literal=username='my-username' \
  --from-literal=password='my-password'
```

## Passing Configuration Data to Pods

### Using Environment Variables

You can pass ConfigMap and Secret data to your containers by referencing them in your Pod's environment variables.

**Example Pod spec with environment variables:**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: env-pod
spec:
  containers:
  - name: app-container
    image: alpine
    command: ["sh", "-c", "echo Welcome $APP_NAME; sleep 3600"]
    env:
    - name: APP_NAME
      valueFrom:
        configMapKeyRef:
          name: my-configmap
          key: app.name
    - name: APP_MESSAGE
      valueFrom:
        secretKeyRef:
          name: my-secret
          key: message
```

### Using Volumes

You can also mount ConfigMaps and Secrets as files within your containers by using volumes.

**Example Pod spec with volumes:**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: volume-pod
spec:
  containers:
  - name: app-container
    image: alpine
    command: ["sh", "-c", "cat /etc/config/app.name; sleep 3600"]
    volumeMounts:
    - name: config-volume
      mountPath: /etc/config
  volumes:
  - name: config-volume
    configMap:
      name: my-configmap
```

## Hands-On Demonstration

### Using ConfigMaps with Environment Variables

1. **Create a ConfigMap:**

   ```yaml
   # configmap.yaml
   apiVersion: v1
   kind: ConfigMap
   metadata:
     name: my-configmap
   data:
     app.name: "MyApp"
     app.message: "Welcome to MyApp!"
   ```

   Apply the ConfigMap:

   ```bash
   kubectl apply -f configmap.yaml
   ```

2. **Create a Pod that uses the ConfigMap:**

   ```yaml
   # env-pod.yaml
   apiVersion: v1
   kind: Pod
   metadata:
     name: env-pod
   spec:
     containers:
     - name: app-container
       image: alpine
       command: ["sh", "-c", "echo $APP_NAME - $APP_MESSAGE; sleep 3600"]
       env:
       - name: APP_NAME
         valueFrom:
           configMapKeyRef:
             name: my-configmap
             key: app.name
       - name: APP_MESSAGE
         valueFrom:
           configMapKeyRef:
             name: my-configmap
             key: app.message
   ```

   Apply the Pod:

   ```bash
   kubectl apply -f env-pod.yaml
   ```

3. **Verify the environment variables:**

   ```bash
   kubectl logs env-pod
   ```

   You should see the output:

   ```
   MyApp - Welcome to MyApp!
   ```

### Using Secrets with Volumes

1. **Create a Secret:**

   ```bash
   kubectl create secret generic my-secret \
     --from-literal=username='admin' \
     --from-literal=password='s3cr3t'
   ```

2. **Create a Pod that mounts the Secret as a volume:**

   ```yaml
   # volume-pod.yaml
   apiVersion: v1
   kind: Pod
   metadata:
     name: volume-pod
   spec:
     containers:
     - name: app-container
       image: alpine
       command: ["sh", "-c", "ls /etc/secret; sleep 3600"]
       volumeMounts:
       - name: secret-volume
         mountPath: "/etc/secret"
         readOnly: true
     volumes:
     - name: secret-volume
       secret:
         secretName: my-secret
   ```

   Apply the Pod:

   ```bash
   kubectl apply -f volume-pod.yaml
   ```

3. **Verify the mounted Secret files:**

   List the files in the secret directory:

   ```bash
   kubectl exec volume-pod -- ls /etc/secret
   ```

   Output:

   ```
   password
   username
   ```

   View the contents of the `username` file:

   ```bash
   kubectl exec volume-pod -- cat /etc/secret/username
   ```

   Output:

   ```
   admin
   ```

### Using ConfigMaps and Secrets Together

You can also use both ConfigMaps and Secrets in the same Pod, either as environment variables or as mounted volumes, depending on your application's needs.

**Example Pod spec:**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: combined-pod
spec:
  containers:
  - name: app-container
    image: alpine
    command: ["sh", "-c", "echo $APP_NAME; cat /etc/secret/password; sleep 3600"]
    env:
    - name: APP_NAME
      valueFrom:
        configMapKeyRef:
          name: my-configmap
          key: app.name
    volumeMounts:
    - name: secret-volume
      mountPath: "/etc/secret"
      readOnly: true
  volumes:
  - name: secret-volume
    secret:
      secretName: my-secret
```

Apply the Pod:

```bash
kubectl apply -f combined-pod.yaml
```

Verify the environment variable and secret file:

```bash
kubectl logs combined-pod
```

Output:

```
MyApp
s3cr3t
```

## Conclusion

Managing application configuration in Kubernetes using ConfigMaps and Secrets allows you to decouple configuration from your container images. This enhances security, portability, and flexibility. By understanding how to create and use ConfigMaps and Secrets, and how to pass this data to your containers, you can effectively manage your application's configuration in a Kubernetes environment.



