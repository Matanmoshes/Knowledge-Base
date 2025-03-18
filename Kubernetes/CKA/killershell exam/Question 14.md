Solve this question on: `ssh cka9412`

Perform some tasks on cluster certificates:

1. Check how long the kube-apiserver server certificate is valid using openssl or cfssl. Write the expiration date into `/opt/course/14/expiration`. Run the `kubeadm` command to list the expiration dates and confirm both methods show the same one
2. Write the `kubeadm` command that would renew the kube-apiserver certificate into `/opt/course/14/kubeadm-renew-certs.sh`

---


## Solution 

Below is a **step‐by‐step** approach to check the **kube-apiserver** server certificate expiration, record it, confirm via **kubeadm**, and then write a command to renew it.

---

## 1. SSH into cka9412

```bash
ssh cka9412
```

---

## 2. Determine the Path to the kube-apiserver Certificate

Typically, the **kube-apiserver** server certificate is located at **`/etc/kubernetes/pki/apiserver.crt`**. Verify if that file exists:

```bash
ls -l /etc/kubernetes/pki/apiserver.crt
```

---

## 3. Check How Long the Certificate Is Valid (Using openssl)

Use `openssl` to parse the **end date**:

```bash
openssl x509 -in /etc/kubernetes/pki/apiserver.crt -noout -enddate
```

This outputs something like:

```
notAfter=Feb 14 09:29:46 2024 GMT
```

We just want the actual date/time.

Alternatively, you could use `cfssl` if installed, but **openssl** is more common in exam environments.

---

## 4. Write the Expiration Date into /opt/course/14/expiration

Assuming you want to store just the date/time (without `notAfter=`), do something like:

```bash
openssl x509 -in /etc/kubernetes/pki/apiserver.crt -noout -enddate \
  | sed 's/notAfter=//' \
  > /opt/course/14/expiration
```

Check the file:

```bash
cat /opt/course/14/expiration
```

It should show something like:

```
Feb 14 09:29:46 2024 GMT
```

---

## 5. Confirm with kubeadm that the Date Matches

Run:

```bash
kubeadm certs check-expiration
```

You should see a table, including something like:

```
CERTIFICATE                EXPIRES                  RESIDUAL TIME   ...
apiserver                  Feb 14 09:29 UTC 2024    364d            ...
```

Confirm it matches the date you found with openssl.

---

## 6. Write the kubeadm Renew Command Into /opt/course/14/kubeadm-renew-certs.sh

To **renew** only the apiserver certificate, you can do:

```bash
echo 'kubeadm cert renew apiserver' > /opt/course/14/kubeadm-renew-certs.sh
chmod +x /opt/course/14/kubeadm-renew-certs.sh
```

This is the main command for just renewing the **apiserver** certificate. Alternatively, if you needed to renew **all** certificates, you’d do `kubeadm cert renew all`.

---

## 7. Summary

1. We used `openssl x509 -in /etc/kubernetes/pki/apiserver.crt -noout -enddate` to retrieve the **expiration** date.
2. We wrote it to **`/opt/course/14/expiration`**.
3. We checked the same date/time using `kubeadm certs check-expiration`.
4. We wrote the **kubeadm** command to renew only the **apiserver** cert (`kubeadm cert renew apiserver`) into **`/opt/course/14/kubeadm-renew-certs.sh`**.