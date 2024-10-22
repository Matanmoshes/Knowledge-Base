# Introduction

Backups are a crucial part of maintaining a resilient and reliable Kubernetes cluster. The **etcd** database is the central data store for Kubernetes, holding all the cluster's state and configuration data. In this guide, I will walk through the steps to back up and restore Kubernetes data stored in etcd. This hands-on practice will help you become comfortable with the backup and restoration process, ensuring you can recover your cluster data in case of failures.

---

# Prerequisites

- Access to the etcd server or control plane node.
- Administrative privileges on the server.
- Knowledge of the file paths for the SSL/TLS certificates used by etcd.
- `etcdctl` command-line tool installed on the server.
- Ensure `ETCDCTL_API` is set to version 3 (`ETCDCTL_API=3`).

---
# Directory Structure

Before we begin, here's the directory structure we'll be working with:

```bash
cloud_user@etcd-1:~$ tree -L 3
.
├── etcd-certs
│   ├── etcd-ca.pem
│   ├── etcd-server.crt
│   └── etcd-server.key
└── etcd_backup.db
```

- **`etcd-certs`**: Contains the SSL/TLS certificates required for secure communication with etcd.
- **`etcd_backup.db`**: The backup file that will be created during the backup process.

---
# Steps Overview

1. [Log in to the Server](#1-log-in-to-the-server)
2. [Verify etcd Data](#2-verify-etcd-data)
3. [Back Up the etcd Data](#3-back-up-the-etcd-data)
4. [Reset etcd Data](#4-reset-etcd-data)
5. [Restore etcd Data from Backup](#5-restore-etcd-data-from-backup)
6. [Set Correct Permissions](#6-set-correct-permissions)
7. [Start etcd Service](#7-start-etcd-service)
8. [Verify Restored Data](#8-verify-restored-data)

---

## 1. Log in to the Server

Use SSH to log in to the provided lab server using the credentials supplied:

```bash
ssh cloud_user@<PUBLIC_IP_ADDRESS>
```

Replace `<PUBLIC_IP_ADDRESS>` with the actual IP address of your etcd server.

---

## 2. Verify etcd Data

Before performing a backup, verify the current data stored in etcd. We'll check the value of the key `cluster.name`:

```bash
ETCDCTL_API=3 etcdctl get cluster.name \
  --endpoints=https://10.0.1.101:2379 \
  --cacert=/home/cloud_user/etcd-certs/etcd-ca.pem \
  --cert=/home/cloud_user/etcd-certs/etcd-server.crt \
  --key=/home/cloud_user/etcd-certs/etcd-server.key
```

**Expected Output:**

```
cluster.name
beebox
```

This confirms that the key `cluster.name` exists and has the value `beebox`.

### Explanation

- **`ETCDCTL_API=3`**: Ensures that `etcdctl` uses API version 3.
- **`etcdctl get cluster.name`**: Retrieves the value associated with the key `cluster.name`.
- **`--endpoints`**: Specifies the etcd server endpoint (`https://10.0.1.101:2379`).
- **`--cacert`, `--cert`, `--key`**: Paths to the SSL/TLS certificate files required for secure communication.

---

## 3. Back Up the etcd Data

Create a snapshot backup of the etcd data using the `etcdctl snapshot save` command:

```bash
ETCDCTL_API=3 etcdctl snapshot save /home/cloud_user/etcd_backup.db \
  --endpoints=https://10.0.1.101:2379 \
  --cacert=/home/cloud_user/etcd-certs/etcd-ca.pem \
  --cert=/home/cloud_user/etcd-certs/etcd-server.crt \
  --key=/home/cloud_user/etcd-certs/etcd-server.key
```

**Expected Output:**

```
Snapshot saved at /home/cloud_user/etcd_backup.db
```

### Explanation

- **`snapshot save /home/cloud_user/etcd_backup.db`**: Saves a snapshot of the etcd data to the specified file.
- The other parameters are the same as in the previous command and ensure secure communication with etcd.

---

## 4. Reset etcd Data

Simulate data loss by removing all existing etcd data:

```bash
sudo systemctl stop etcd
sudo rm -rf /var/lib/etcd
```

### Explanation

- **`sudo systemctl stop etcd`**: Stops the etcd service to prevent it from accessing the data directory during deletion.
- **`sudo rm -rf /var/lib/etcd`**: Deletes the etcd data directory, effectively wiping all stored data.

---

## 5. Restore etcd Data from Backup

Restore the etcd data from the backup snapshot:

```bash
sudo ETCDCTL_API=3 etcdctl snapshot restore /home/cloud_user/etcd_backup.db \
  --initial-cluster etcd-restore=https://10.0.1.101:2380 \
  --initial-advertise-peer-urls https://10.0.1.101:2380 \
  --name etcd-restore \
  --data-dir /var/lib/etcd
```

### Explanation

- **`snapshot restore /home/cloud_user/etcd_backup.db`**: Restores etcd data from the specified snapshot file.
- **`--initial-cluster etcd-restore=https://10.0.1.101:2380`**: Defines the initial cluster configuration for the restored etcd node.
- **`--initial-advertise-peer-urls https://10.0.1.101:2380`**: Specifies the URL through which the restored etcd node will advertise to peers.
- **`--name etcd-restore`**: Sets the name of the restored etcd node.
- **`--data-dir /var/lib/etcd`**: Specifies the data directory where the restored data will be stored.

**Note**: We are restoring the data to the original data directory (`/var/lib/etcd`).

---

## 6. Set Correct Permissions

After restoring the data, set the correct ownership on the new data directory:

```bash
sudo chown -R etcd:etcd /var/lib/etcd
```

### Explanation

- **`sudo chown -R etcd:etcd /var/lib/etcd`**: Recursively changes the owner and group of the `/var/lib/etcd` directory to `etcd`, ensuring the etcd service has the necessary permissions.

---

## 7. Start etcd Service

Start the etcd service to apply the restored data:

```bash
sudo systemctl start etcd
```

---

## 8. Verify Restored Data

Check that the etcd data has been successfully restored by retrieving the value of `cluster.name` again:

```bash
ETCDCTL_API=3 etcdctl get cluster.name \
  --endpoints=https://10.0.1.101:2379 \
  --cacert=/home/cloud_user/etcd-certs/etcd-ca.pem \
  --cert=/home/cloud_user/etcd-certs/etcd-server.crt \
  --key=/home/cloud_user/etcd-certs/etcd-server.key
```

**Expected Output:**

```
cluster.name
beebox
```

This confirms that the data has been restored successfully.

---

## Summary

In this guide, I performed the following steps:

1. **Verified the existing etcd data** by checking the `cluster.name` key.
2. **Created a backup** of the etcd data using `etcdctl snapshot save`.
3. **Simulated data loss** by stopping etcd and deleting the data directory.
4. **Restored the etcd data** from the backup using `etcdctl snapshot restore`.
5. **Set the correct permissions** on the restored data directory.
6. **Started the etcd service** to apply the restored data.
7. **Verified the restoration** by checking the `cluster.name` key again.

---

## Important Considerations

- **SSL/TLS Certificates**: Ensure the paths to the certificate files are correct and that you have the necessary permissions to read them.
- **Data Directory Permissions**: Always set the correct ownership and permissions on the etcd data directory after restoration.
- **Service Management**: Use `systemctl` to manage the etcd service, ensuring it is stopped before making changes and started after restoration.
- **Backup Regularly**: Schedule regular backups of etcd data to prevent data loss.
- **Test Restores**: Periodically test the restore process to ensure backups are valid and the restoration process works as expected.

---

## Conclusion

Backing up and restoring etcd data is essential for maintaining the integrity and availability of your Kubernetes cluster. By practicing these steps, you become better prepared to handle potential data loss scenarios, ensuring minimal downtime and consistent cluster operation.

---

**Next Steps:**

- **Automate Backups**: Consider creating scripts or cron jobs to automate the backup process.
- **Secure Backup Storage**: Store backups in a secure, off-site location to protect against node or site failures.
- **Documentation**: Keep detailed documentation of your backup and restore procedures for reference during emergencies.

---

