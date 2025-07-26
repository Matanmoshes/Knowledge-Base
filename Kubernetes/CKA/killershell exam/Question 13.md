Solve this question on: `ssh cka7968`

The team from Project r500 wants to replace their Ingress (networking.k8s.io) with a Gateway Api (gateway.networking.k8s.io) solution. The old Ingress is available at `/opt/course/13/ingress.yaml`.

Perform the following in _Namespace_ `project-r500` and for the already existing _Gateway_:

1. Create a new _HTTPRoute_ named `traffic-director` which replicates the routes from the old Ingress
2. Extend the new _HTTPRoute_ with path `/auto` which redirects to mobile if the User-Agent is exactly `mobile` and to desktop otherwise

The existing _Gateway_ is reachable at `http://r500.gateway:30080` which means your implementation should work for these commands:

```bash
curl r500.gateway:30080/desktop
curl r500.gateway:30080/mobile
curl r500.gateway:30080/auto -H "User-Agent: mobile" 
curl r500.gateway:30080/auto
```


----

## Solution

Below is a **step‐by‐step** approach to replicate the old Ingress routes as an **HTTPRoute** named `traffic-director` in the `project-r500` namespace, and add the new `/auto` path logic. We’ll assume the existing Gateway is called **`r500-gateway`** in the same namespace. If the Gateway name is different, adjust accordingly.

---

![[Screenshot 2025-03-17 at 21.30.39.png]]


## 1. Check Existing Gateway

If you’re unsure of the existing Gateway’s name or namespace, run:

```bash
kubectl get gateway -A
```

Suppose it’s in `project-r500` and named `r500-gateway`. That’s what we’ll reference in the HTTPRoute’s `parentRefs`.

---

## 2. Replicate Old Ingress Routes

From the old Ingress (at `/opt/course/13/ingress.yaml`), you likely have something like:

```yaml
- path: /desktop    -> desktop-svc:80
- path: /mobile     -> mobile-svc:80
```

We’ll replicate these as rules in the new HTTPRoute.

---

## 3. Extend With `/auto` Path

**`/auto`** must route to **`mobile`** backend if `User-Agent: mobile`, else to **`desktop`**. In Gateway API, we do **two separate rules** for the same path prefix `/auto`, but with different **`headers:`** matches or no match.

---

## 4. Create the `HTTPRoute` Manifest

Create a file, for example, `traffic-director.yaml`:

```yaml
apiVersion: gateway.networking.k8s.io/v1beta1
kind: HTTPRoute
metadata:
  name: traffic-director
  namespace: project-r500
spec:
  parentRefs:
  - name: r500-gateway           # the existing Gateway name
    # if the Gateway is in project-r500, no need for 'namespace:' here
  rules:
    # 1) /desktop -> desktop-svc:80
    - matches:
      - path:
          type: PathPrefix
          value: /desktop
      backendRefs:
      - name: desktop-svc
        port: 80

    # 2) /mobile -> mobile-svc:80
    - matches:
      - path:
          type: PathPrefix
          value: /mobile
      backendRefs:
      - name: mobile-svc
        port: 80

    # 3) /auto + header User-Agent=mobile -> mobile-svc:80
    - matches:
      - path:
          type: PathPrefix
          value: /auto
        headers:
          - name: User-Agent
            value: mobile          # exact match
      backendRefs:
      - name: mobile-svc
        port: 80

    # 4) /auto (no special header) -> desktop-svc:80
    - matches:
      - path:
          type: PathPrefix
          value: /auto
      backendRefs:
      - name: desktop-svc
        port: 80
```

### Explanation

1. **`parentRefs:`** → references the existing Gateway named `r500-gateway` in `project-r500`.
2. **`/desktop`** → goes to `desktop-svc:80`.
3. **`/mobile`** → goes to `mobile-svc:80`.
4. **`/auto`** with header `User-Agent=mobile` → `mobile-svc:80`.
5. **`/auto`** otherwise → `desktop-svc:80`.

---

## 5. Apply the HTTPRoute

On **`cka7968`**:

```bash
kubectl apply -f traffic-director.yaml
```

Check if it’s created:

```bash
kubectl get httproute traffic-director -n project-r500 -o yaml
```

You should see that it’s **Accepted** and references `r500-gateway`.

---

## 6. Test the Routes

Because the question says the Gateway is reachable at `http://r500.gateway:30080`, test:

```bash
# /desktop
curl r500.gateway:30080/desktop

# /mobile
curl r500.gateway:30080/mobile

# /auto with user-agent=mobile -> mobile route
curl r500.gateway:30080/auto -H "User-Agent: mobile"

# /auto with default user-agent -> desktop route
curl r500.gateway:30080/auto
```

You should see the correct backends responding. If you have logs in the `desktop-svc` or `mobile-svc` pods, you can confirm requests are reaching them.

---

## 7. Summary

You have:

1. Removed (or replaced) the old Ingress with an **HTTPRoute** named `traffic-director`.
2. Replicated the `/desktop` and `/mobile` routes.
3. Added the new `/auto` logic:
    - **User-Agent=mobile** → `mobile-svc`
    - Else → `desktop-svc`
4. Tested that the traffic routes via the existing Gateway at `r500.gateway:30080`.
