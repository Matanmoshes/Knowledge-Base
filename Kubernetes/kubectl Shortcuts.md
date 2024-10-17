# **kubectl Shortcuts for Common Commands**

### **Get Commands**  
| Command                          | Description                              |
|----------------------------------|------------------------------------------|
| `kubectl get ns`                 | List all namespaces                     |
| `kubectl get no`                 | List all nodes                          |
| `kubectl get po`                 | List all pods in the current namespace  |
| `kubectl get svc`                | List all services                       |
| `kubectl get cm`                 | List all ConfigMaps                     |
| `kubectl get ep`                 | List all endpoints                      |
| `kubectl get ing`                | List all Ingress resources              |
| `kubectl get ds`                 | List all DaemonSets                     |
| `kubectl get deploy`             | List all deployments                    |
| `kubectl get sts`                | List all StatefulSets                   |
| `kubectl get rs`                 | List all ReplicaSets                    |
| `kubectl get crd`                | List all custom resource definitions    |
| `kubectl get pv`                 | List all persistent volumes             |
| `kubectl get pvc`                | List all persistent volume claims       |
| `kubectl get sa`                 | List all service accounts               |
| `kubectl get netpol`             | List all network policies               |

---

### **Describe Commands**  
| Command                          | Description                              |
|----------------------------------|------------------------------------------|
| `kubectl describe po <pod>`      | Describe a specific pod                 |
| `kubectl describe svc <service>` | Describe a specific service             |
| `kubectl describe deploy <name>` | Describe a specific deployment          |
| `kubectl describe ns <name>`     | Describe a specific namespace           |
| `kubectl describe node <name>`   | Describe a specific node                |
| `kubectl describe pv <name>`     | Describe a persistent volume            |
| `kubectl describe pvc <name>`    | Describe a persistent volume claim      |

---

### **Create Commands**  
| Command                               | Description                            |
|---------------------------------------|----------------------------------------|
| `kubectl create ns <name>`            | Create a new namespace                |
| `kubectl create deploy <name> --image=<image>` | Create a deployment          |
| `kubectl create svc clusterip <name> --tcp=80:80` | Create a ClusterIP service |
| `kubectl create configmap <name> --from-literal=key=value` | Create ConfigMap |
| `kubectl create sa <name>`            | Create a service account              |

---

### **Delete Commands**  
| Command                          | Description                              |
|----------------------------------|------------------------------------------|
| `kubectl delete ns <name>`       | Delete a namespace                      |
| `kubectl delete po <pod>`        | Delete a specific pod                   |
| `kubectl delete deploy <name>`   | Delete a specific deployment            |
| `kubectl delete svc <name>`      | Delete a specific service               |
| `kubectl delete cm <name>`       | Delete a ConfigMap                      |

---

### **Logs and Exec Commands**  
| Command                                    | Description                               |
|--------------------------------------------|-------------------------------------------|
| `kubectl logs po/<pod>`                    | View logs of a pod                        |
| `kubectl logs po/<pod> -f`                 | Stream logs in real-time                  |
| `kubectl exec -it <pod> -- /bin/bash`      | Open a shell inside the pod               |
| `kubectl exec <pod> -- <command>`          | Run a command inside the pod              |

---

### **Rollout and Scale Commands**  
| Command                                      | Description                              |
|----------------------------------------------|------------------------------------------|
| `kubectl rollout status deploy/<name>`       | Check rollout status of a deployment     |
| `kubectl rollout undo deploy/<name>`         | Rollback to the previous deployment      |
| `kubectl scale deploy <name> --replicas=3`   | Scale a deployment to 3 replicas         |

---

### **Namespace Commands**  
| Command                          | Description                              |
|----------------------------------|------------------------------------------|
| `kubectl get ns`                 | List all namespaces                     |
| `kubectl create ns <name>`       | Create a new namespace                  |
| `kubectl delete ns <name>`       | Delete a namespace                      |
| `kubectl config set-context --current --namespace=<name>` | Set the current namespace |

---

### **Apply and Edit Commands**  
| Command                                    | Description                               |
|--------------------------------------------|-------------------------------------------|
| `kubectl apply -f <file.yaml>`             | Apply configuration from a YAML file     |
| `kubectl edit deploy <name>`               | Edit a deployment in-place               |

---

### **Top (Metrics) Commands**  
| Command                          | Description                              |
|----------------------------------|------------------------------------------|
| `kubectl top nodes`              | Show metrics for all nodes              |
| `kubectl top pods`               | Show metrics for all pods               |

---

### **Label and Annotate Commands**  
| Command                                          | Description                              |
|--------------------------------------------------|------------------------------------------|
| `kubectl label po <pod> env=prod`                | Add a label to a pod                    |
| `kubectl annotate po <pod> description='test'`   | Add an annotation to a pod              |

---

### **Completion (Auto-completion for Bash/Zsh)**  
```bash
source <(kubectl completion bash)  # Bash completion
source <(kubectl completion zsh)   # Zsh completion
```

---

This cheat sheet provides a quick reference to **kubectl shortcut commands**, making it easy to manage your Kubernetes clusters. Save it for quick access!