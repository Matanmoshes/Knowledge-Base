Solve this question on: `ssh cka3962`

Create a new _ServiceAccount_ `processor` in _Namespace_ `project-hamster`. Create a _Role_ and _RoleBinding_, both named `processor` as well. These should allow the new _SA_ to only create _Secrets_ and _ConfigMaps_ in that _Namespace_.

---

## Solution


Below are the **imperative commands** to create the **ServiceAccount**, **Role**, and **RoleBinding**, all named **`processor`** in the namespace **`project-hamster`**, granting only **create** permissions on **Secrets** and **ConfigMaps**:

---

## 1. Create the ServiceAccount

```bash
kubectl create serviceaccount processor --namespace project-hamster
```

Check it:

```bash
kubectl get serviceaccounts -n project-hamster
```

---

## 2. Create the Role (named processor)

Use the `create role` command to allow `create` on **Secrets** and **ConfigMaps**:

```bash
kubectl create role processor \
  --verb=create \
  --resource=secrets,configmaps \
  --namespace project-hamster
```

Check it:

```bash
kubectl get role processor -n project-hamster -o yaml
```

---

## 3. Create the RoleBinding (named processor)

Bind the **processor** Role to the **processor** ServiceAccount:

```bash
kubectl create rolebinding processor \
  --role=processor \
  --serviceaccount=project-hamster:processor \
  --namespace project-hamster
```

Check it:

```bash
kubectl get rolebinding processor -n project-hamster -o yaml
```

---

## 4. Verify (Optional)

You can test the new permissions, for example:

```bash
kubectl auth can-i create secrets \
  --as system:serviceaccount:project-hamster:processor \
  -n project-hamster
```

Should return **`yes`**, confirming the new **processor** ServiceAccount can create Secrets (and also ConfigMaps) but no other resource actions.