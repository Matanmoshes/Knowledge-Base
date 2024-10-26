Here’s a detailed guide on using ConfigMaps and Secrets in a Kubernetes Deployment. This example will demonstrate how to inject non-sensitive and sensitive data into a `todo-app` container using ConfigMaps and Secrets.

---

## Using ConfigMaps and Secrets in a Kubernetes Deployment

### Overview

In Kubernetes, **ConfigMaps** are used to inject configuration data, and **Secrets** are used for sensitive data, such as passwords or API keys. In this guide, we’ll:
1. Create a ConfigMap to hold non-sensitive configuration data.
2. Create a Secret to hold sensitive information.
3. Define a Deployment that uses both the ConfigMap and Secret to inject environment variables and mount them as files in the container.

### Step 1: Create a ConfigMap

Let's start by creating a ConfigMap to store general configuration settings for the app.

1. **Create the ConfigMap YAML file** (`configmap.yml`):

   ```yaml
   apiVersion: v1
   kind: ConfigMap
   metadata:
     name: todo-app-config
   data:
     APP_NAME: "My To-Do Application"
   ```

2. **Apply the ConfigMap**:

   ```bash
   kubectl apply -f configmap.yml
   ```

   This command creates a ConfigMap named `todo-app-config` with one key-value pair:
   - `APP_NAME`: The name of the application.

### Step 2: Create a Secret

Next, create a Secret for storing sensitive information, such as a database password.

1. **Create the Secret YAML file** (`secret.yml`):

   ```yaml
   apiVersion: v1
   kind: Secret
   metadata:
     name: todo-app-secret
   type: Opaque
   data:
     DB_PASSWORD: bXlfc2VjdXJlcF9wYXNzd29yZA==  # base64 encoded "my_secure_password"
   ```

   - The value for `DB_PASSWORD` is base64 encoded. You can generate this by running:
     ```bash
     echo -n "my_secure_password" | base64
     ```

2. **Apply the Secret**:

   ```bash
   kubectl apply -f secret.yml
   ```

   This creates a Secret named `todo-app-secret` with a key-value pair:
   - `DB_PASSWORD`: The database password (encoded).

### Step 3: Create a Deployment that Uses ConfigMap and Secret

Now, create a Deployment for the `todo-app` that uses both the ConfigMap and Secret.

1. **Create the Deployment YAML file** (`deployment.yml`):

   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: todo-app-deployment
   spec:
     selector:
       matchLabels:
         app: todo-app
     template:
       metadata:
         labels:
           app: todo-app
       spec:
         containers:
         - name: todo-app-container
           image: matanm66/to-do-app:v1
           ports:
             - containerPort: 8501
           env:
             - name: APP_NAME
               valueFrom:
                 configMapKeyRef:
                   name: todo-app-config
                   key: APP_NAME
             - name: DB_PASSWORD
               valueFrom:
                 secretKeyRef:
                   name: todo-app-secret
                   key: DB_PASSWORD
           volumeMounts:
             - name: config-volume
               mountPath: "/etc/config"
             - name: secret-volume
               mountPath: "/etc/secret"
               readOnly: true
         volumes:
           - name: config-volume
             configMap:
               name: todo-app-config
           - name: secret-volume
             secret:
               secretName: todo-app-secret
   ```

   - **Environment Variables**: 
     - `APP_NAME`: Injected from the ConfigMap `todo-app-config`.
     - `DB_PASSWORD`: Injected from the Secret `todo-app-secret`.
   - **Volumes**:
     - **ConfigMap Volume**: Mounted at `/etc/config` so that any keys in the ConfigMap (like `APP_NAME`) are accessible as files.
     - **Secret Volume**: Mounted at `/etc/secret` to store sensitive data (like `DB_PASSWORD`) as files. It’s marked `readOnly` for security.

2. **Apply the Deployment**:

   ```bash
   kubectl apply -f deployment.yml
   ```

   This creates the Deployment `todo-app-deployment` and deploys the `todo-app` container with the configuration and secret data injected.

### Step 4: Verify the Deployment

1. **Check the Pod Status**:

   ```bash
   kubectl get pods
   ```

   Ensure that the pod for the `todo-app-deployment` is running.

2. **Verify Environment Variables**:

   Run the following command to see if environment variables are set correctly in the container:

   ```bash
   kubectl exec -it <pod-name> -- printenv APP_NAME DB_PASSWORD
   ```

   Replace `<pod-name>` with the actual pod name of your `todo-app` deployment. You should see the values for `APP_NAME` and `DB_PASSWORD`.

3. **Verify Mounted Files**:

   - **ConfigMap File**:
     ```bash
     kubectl exec -it <pod-name> -- cat /etc/config/APP_NAME
     ```
   - **Secret File**:
     ```bash
     kubectl exec -it <pod-name> -- cat /etc/secret/DB_PASSWORD
     ```

   These commands should display the content from the ConfigMap and Secret files mounted in the container.

---

### Summary

You’ve successfully created a Kubernetes Deployment that uses ConfigMaps and Secrets to manage configuration and sensitive data in your `todo-app` container. You used:

- **ConfigMaps** to store non-sensitive configuration as environment variables and files.
- **Secrets** to store sensitive data as environment variables and read-only files.
- **Volumes** to mount ConfigMaps and Secrets as files in the container.

