Solve this question on: `ssh cka3962`

There are two _Pods_ named `o3db-*` in _Namespace_ `project-h800`. The Project H800 management asked you to scale these down to one replica to save resources.

---

## Solution 


``` bash
kubectl scale --curent-replicas=2 --replicas=1 replicaset/o3db-* -n project-h800
```


