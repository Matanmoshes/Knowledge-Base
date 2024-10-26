Here’s a guide to help you view your `to-do-app` application in Minikube:

---

## Accessing the `to-do-app` Deployment in Minikube

### Prerequisites
- Minikube is installed and running with the Docker driver.
- You have a Kubernetes deployment named `to-do-app` running in your Minikube cluster.

### Step 1: Create a Service for the `to-do-app`

1. **Define the Service**:
   Create a YAML file named `service.yml` with the following content to expose your `to-do-app`:

   ```yaml
   apiVersion: v1
   kind: Service
   metadata:
     name: to-do-app-service
   spec:
     selector:
       app: to-do-app
     ports:
       - protocol: TCP
         port: 80           # Port accessible on the Service
         targetPort: 8501    # Port exposed by the container
     type: NodePort
   ```

   - **`port`**: This is the port through which the service will be accessible.
   - **`targetPort`**: This matches the container’s exposed port (`8501` in this case).
   - **`type: NodePort`**: This makes the service accessible on a port assigned by Minikube.

2. **Apply the Service**:
   Run the following command to create the service:

   ```bash
   kubectl apply -f service.yml
   ```

   This command will create a service named `to-do-app-service`.

### Step 2: Expose the Service using Minikube

1. **Open the Service in Your Browser**:
   Use the `minikube service` command to access the service in your default web browser:

   ```bash
   minikube service to-do-app-service
   ```

   Minikube will open the service in a new browser tab if the command succeeds.

2. **Retrieve the Service URL (alternative)**:
   If the command above does not open the browser automatically, you can get the URL manually:

   ```bash
   minikube service to-do-app-service --url
   ```

   Copy the URL from the output and paste it into your browser to view the `to-do-app` application.
