Solve this question on: `ssh cka2556`

In _Namespace_ `project-tiger` create a _Pod_ named `tigers-reunite` of image `httpd:2-alpine` with labels `pod=container` and `container=pod`. Find out on which node the _Pod_ is scheduled. Ssh into that node and find the containerd container belonging to that _Pod_.

Using command `crictl`:

1. Write the ID of the container and the `info.runtimeType` into `/opt/course/17/pod-container.txt`
    
2. Write the logs of the container into `/opt/course/17/pod-container.log`
    

> ℹ️ You can connect to a worker node using `ssh cka2556-node1` or `ssh cka2556-node2` from `cka2556`

---

## Solution

Below is a **step‐by‐step** approach to create a Pod in the **`project-tiger`** namespace, find out which node it’s scheduled on, SSH to that node, identify the container using `crictl`, and save both the container’s ID/runtimeType and its logs.

---

## 1. Create the Pod `tigers-reunite`

1. Ensure the **`project-tiger`** namespace exists:
    
    ```bash
    kubectl create namespace project-tiger
    ```
    
2. Create a single‐container Pod named **`tigers-reunite`** of image `httpd:2-alpine` with the specified labels:
    
    ```bash
    kubectl run tigers-reunite \
      -n project-tiger \
      --image=httpd:2-alpine \
      --restart=Never \
      --labels='pod=container,container=pod'
    ```
    
3. Wait for the Pod to be **Running**:
    
    ```bash
    kubectl get pods -n project-tiger -w
    ```
    

---

## 2. Identify Which Node the Pod is on

```bash
kubectl get pods -n project-tiger -o wide
```

You’ll see something like:

```
NAME              READY   STATUS    RESTARTS   AGE   IP            NODE                ...
tigers-reunite    1/1     Running   0          30s   10.244.2.15   cka2556-node1       ...
```

Let’s assume it’s on **`cka2556-node1`**.

---

## 3. SSH to the Node Where the Pod is Running

From the control plane (`cka2556`):

```bash
ssh cka2556-node1
```

---

## 4. Find the Container Using `crictl`

1. **List** containers:
    
    ```bash
    sudo crictl ps -a
    ```
    
2. Look for a container whose **`NAME`** or `LABELS` mention **`tigers-reunite`** or **`httpd:2-alpine`**.  
    Something like:
    
    ```
    CONTAINER           IMAGE               CREATED         STATE    NAME                          ATTEMPT   POD ID
    a9350d961ce6f       2-alpine            30 seconds ago  Running  tigers-reunite                0         ...
    ```
    
    The **`CONTAINER`** column (e.g. `a9350d961ce6f`) is your container ID.

---

## 5. Extract Container ID & runtimeType

1. **Container ID** is, e.g., `a9350d961ce6f`.
2. **Inspect** to see the `runtimeType`:
    
    ```bash
    sudo crictl inspect a9350d961ce6f | grep runtimeType
    ```
    
    or run a full inspect:
    
    ```bash
    sudo crictl inspect a9350d961ce6f
    ```
    
    Typically, you’ll see `"runtimeType": "io.containerd.runc.v2"` or similar.

Write them into **`/opt/course/17/pod-container.txt`** (on the node). For example:

```bash
echo "a9350d961ce6f io.containerd.runc.v2" > /opt/course/17/pod-container.txt
```

(Adjust the actual container ID and runtimeType found.)

---

## 6. Write the Container Logs to `/opt/course/17/pod-container.log`

Finally, do:

```bash
sudo crictl logs a9350d961ce6f > /opt/course/17/pod-container.log
```

Check:

```bash
cat /opt/course/17/pod-container.log
```

It should show the logs from the `httpd:2-alpine` container (if any logs exist).

---

## 7. Summary

1. **Created** a Pod named `tigers-reunite` in `project-tiger` with labels `pod=container` and `container=pod`.
2. **Identified** which node it’s on via `kubectl get pods -o wide`.
3. **SSH’d** into that node, found the container via `sudo crictl ps`.
4. **Saved** the container’s ID and `info.runtimeType` in `/opt/course/17/pod-container.txt`.
5. **Redirected** container logs to `/opt/course/17/pod-container.log`.