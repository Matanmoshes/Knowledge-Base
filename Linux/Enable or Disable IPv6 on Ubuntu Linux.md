### Guide: How to Enable or Disable IPv6 on Ubuntu Linux (with Backup)

Before making changes to critical configuration files, such as the GRUB configuration file, it's important to create a backup to ensure you can restore it if something goes wrong. This guide will show you how to safely back up the GRUB configuration file before enabling or disabling IPv6 on Ubuntu Linux.

---

### **Step 1: Check Current IPv6 Configuration**

Before making any changes, check whether IPv6 is enabled or disabled on your system. Use the following command:

```bash
ip -6 addr show
```

- If you see `inet6`, IPv6 is **enabled**.
- If you see no output, IPv6 is **disabled**.

---

### **Step 2: Back Up the GRUB Configuration File**

Before editing the GRUB configuration file, it's crucial to create a backup to restore in case of any issues.

1. Run the following command to create a backup of the GRUB configuration file:

```bash
sudo cp /etc/default/grub /etc/default/grub.bak
```

This will create a copy of the GRUB configuration file named `grub.bak` in the same directory. If something goes wrong during editing, you can restore this backup.

---

### **Step 3: Open the GRUB Configuration File**

Now that you’ve backed up the file, you can safely edit it.

1. Use the following command to open the GRUB configuration file in the `nano` text editor:

```bash
sudo nano /etc/default/grub
```

---

### **Step 4: Disable or Enable IPv6**

In the GRUB configuration file, you can enable or disable IPv6 by modifying the `GRUB_CMDLINE_LINUX_DEFAULT` line.

#### **To Disable IPv6:**

1. Locate the line that starts with `GRUB_CMDLINE_LINUX_DEFAULT` and modify it to include the `ipv6.disable=1` option:

```bash
GRUB_CMDLINE_LINUX_DEFAULT="ipv6.disable=1"
```

#### **To Enable IPv6:**

1. If you want to enable IPv6 (or reverse the change), set the `ipv6.disable=0` option:

```bash
GRUB_CMDLINE_LINUX_DEFAULT="ipv6.disable=0"
```

After making your changes, save the file by pressing **Ctrl+O**, then press **Enter**, and finally, exit by pressing **Ctrl+X**.

---

### **Step 5: Update GRUB**

After modifying the GRUB file, apply the changes by updating GRUB:

```bash
sudo update-grub
```

You should see a `done` message indicating that the update was successful.

---

### **Step 6: Reboot the System**

To apply the changes, reboot your Ubuntu system:

```bash
sudo reboot
```

---

### **Step 7: Verify IPv6 Status**

Once the system reboots, check the IPv6 status again to confirm whether it's enabled or disabled:

```bash
ip -6 addr show
```

- **No output** means IPv6 is disabled.
- **`inet6` output** means IPv6 is enabled.

---

### **Restoring from Backup (If Necessary)**

If you encounter any issues after modifying the GRUB configuration file, you can restore the backup:

```bash
sudo cp /etc/default/grub.bak /etc/default/grub
```

Then update GRUB and reboot the system:

```bash
sudo update-grub
sudo reboot
```

---

### **Conclusion**

Disabling or enabling IPv6 on Ubuntu Linux can be done easily by modifying the GRUB configuration file. However, creating a backup before making any changes is essential for safety. This guide provides the steps to safely modify the GRUB configuration file and ensure that IPv6 is configured according to your needs.