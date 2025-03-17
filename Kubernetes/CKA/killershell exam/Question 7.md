Solve this question on: `ssh cka5774`

The metrics-server has been installed in the cluster. Write two bash scripts which use `kubectl`:

1. Script `/opt/course/7/node.sh` should show resource usage of _Nodes_
2. Script `/opt/course/7/pod.sh` should show resource usage of _Pods_ and their containers

---

## Solution

Below is a **step‐by‐step** approach to create the two required scripts on **`cka5774`**:

---

## 1. SSH into cka5774

```bash
ssh cka5774
```

---

## 2. Create the Script for Node Usage

1. **Edit** or create `/opt/course/7/node.sh`:
    
    ```bash
    vi /opt/course/7/node.sh
    ```
    
2. **Add** the following lines:
    
    ```bash
    #!/bin/bash
    # This script shows resource usage of Nodes
    kubectl top node
    ```
    
3. **Save and exit**, then make it **executable**:
    
    ```bash
    chmod +x /opt/course/7/node.sh
    ```
    

---

## 3. Create the Script for Pod Usage

1. **Edit** or create `/opt/course/7/pod.sh`:
    
    ```bash
    vi /opt/course/7/pod.sh
    ```
    
2. **Add** the following lines:
    
    ```bash
    #!/bin/bash
    # This script shows resource usage of Pods (including container-level usage)
    kubectl top pod --containers
    ```
    
3. **Save and exit**, then make it **executable**:
    
    ```bash
    chmod +x /opt/course/7/pod.sh
    ```
    

---

## 4. Test the Scripts

- **Node usage**:
    
    ```bash
    /opt/course/7/node.sh
    ```
    
    You should see something like:
    
    ```
    NAME            CPU(cores)   MEMORY(bytes)
    cka5774-node1   300m         512Mi
    ...
    ```
    
- **Pod usage**:
    
    ```bash
    /opt/course/7/pod.sh
    ```
    
    You should see lines for each pod and **container** with its CPU and memory usage:
    
    ```
    NAMESPACE   NAME                    CONTAINER   CPU(cores)   MEMORY(bytes)
    default     my-app-xyz-123         app         50m          128Mi
    ...
    ```
    

That completes the requirement: two bash scripts that show resource usage for nodes and pods/containers.