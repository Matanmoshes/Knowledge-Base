## Guide for Adding and Managing LVM Volumes on CentOS

This guide will walk you through the process of creating an LVM volume, mounting it to a specific directory, making the mount persistent across reboots, and adding a user to a group. All commands and outputs are based on a real scenario, making it easy to follow and apply in your own environment.

---

## 1. **Creating the Group and Adding a User to It**

We begin by creating a group called `dba_users` and adding the user `bob` to it.

#### Steps:

1. **Create the group:**
   Run the following command to create a new group:
   ```bash
   sudo groupadd dba_users
   ```

2. **Add user `bob` to the group:**
   Use the following command to add `bob` to the `dba_users` group:
   ```bash
   sudo usermod -aG dba_users bob
   ```

3. **Verify that the user was added to the group:**
   To check if `bob` is part of the `dba_users` group, run:
   ```bash
   groups bob
   ```

   **Example output:**
   ```bash
   bob : bob dba_users
   ```

4. **Check the group information:**
   Use this command to verify the group and its members:
   ```bash
   getent group dba_users
   ```

   **Example output:**
   ```bash
   dba_users:x:1003:bob
   ```

---

## 2. **Creating and Mounting an LVM Volume**

We will create a Logical Volume (LV) using four disks, format it, and mount it at `/mnt/dba_storage`.

#### Steps:

1. **Create Physical Volumes:**
   Convert the available disks into physical volumes for LVM:
   ```bash
   sudo pvcreate /dev/vdb /dev/vdc /dev/vdd /dev/vde
   ```

   **Example output:**
   ```bash
   Physical volume "/dev/vdb" successfully created.
   Physical volume "/dev/vdc" successfully created.
   Physical volume "/dev/vdd" successfully created.
   Physical volume "/dev/vde" successfully created.
   ```

2. **Create a Volume Group (VG):**
   Create a volume group named `db_vg`:
   ```bash
   sudo vgcreate db_vg /dev/vdb /dev/vdc /dev/vdd /dev/vde
   ```

   **Example output:**
   ```bash
   Volume group "db_vg" successfully created
   ```

3. **Create a Logical Volume (LV):**
   Create a 3GB logical volume named `db_lv` in the `db_vg` volume group:
   ```bash
   sudo lvcreate -L 3G -n db_lv db_vg
   ```

   **Example output:**
   ```bash
   Logical volume "db_lv" created.
   ```

4. **Format the Logical Volume:**
   Format the logical volume with the `ext4` filesystem:
   ```bash
   sudo mkfs.ext4 /dev/db_vg/db_lv
   ```

   **Example output:**
   ```bash
   Creating filesystem with 786432 4k blocks and 196608 inodes
   Filesystem UUID: 934a2d2d-6fb2-48ea-81db-9e10c0c4a95d
   ```

5. **Create a Mount Point:**
   Create the directory `/mnt/dba_storage` to serve as the mount point for the logical volume:
   ```bash
   sudo mkdir /mnt/dba_storage
   ```

6. **Mount the Logical Volume:**
   Mount the logical volume to the `/mnt/dba_storage` directory:
   ```bash
   sudo mount /dev/db_vg/db_lv /mnt/dba_storage
   ```

7. **Verify the Mount:**
   Use `df -h` to verify that the logical volume is mounted correctly:
   ```bash
   df -h
   ```

   **Example output:**
   ```bash
   /dev/mapper/db_vg-db_lv  2.9G   24K  2.8G   1% /mnt/dba_storage
   ```

---

## 3. **Making the Mount Persistent Across Reboots**

To ensure that the logical volume mounts automatically after a reboot, we need to modify the `/etc/fstab` file.

#### Steps:

1. **Find the UUID of the Logical Volume:**
   Use `blkid` to find the UUID of the logical volume:
   ```bash
   sudo blkid /dev/db_vg/db_lv
   ```

   **Example output:**
   ```bash
   /dev/db_vg/db_lv: UUID="934a2d2d-6fb2-48ea-81db-9e10c0c4a95d" TYPE="ext4"
   ```

2. **Edit `/etc/fstab`:**
   Add the following line to the `/etc/fstab` file to mount the logical volume at `/mnt/dba_storage` on every reboot:
   ```bash
   UUID=934a2d2d-6fb2-48ea-81db-9e10c0c4a95d   /mnt/dba_storage   ext4   defaults   0   0
   ```

3. **Reload the system configuration:**
   After editing `/etc/fstab`, reload the configuration with:
   ```bash
   sudo systemctl daemon-reload
   ```

4. **Test the Mount Persistence:**
   Reboot the system and check if the mount persists:
   ```bash
   sudo reboot
   ```

   After rebooting, verify that the filesystem is still mounted:
   ```bash
   df -h
   ```

   **Example output:**
   ```bash
   /dev/mapper/db_vg-db_lv  2.9G   24K  2.8G   1% /mnt/dba_storage
   ```

---

## Conclusion

By following this guide, you've successfully created a group, added a user to it, set up an LVM volume, mounted it to `/mnt/dba_storage`, and ensured it persists across reboots. This process can now be applied to other tasks involving LVM management, group administration, and persistent storage configuration.

This guide can be further customized based on your organization's needs or specific system configurations. Let me know if you need further assistance!