Configuring and managing swap space in Linux involves creating a swap partition or swap file, enabling it, and configuring system settings to use it. Here's how you can do this:

>[!Swap]
>Swap space in Linux is a designated area on the hard drive used to extend the physical RAM. When the system's RAM is full, inactive memory pages are moved to the swap space to free up RAM for active processes. This helps prevent system crashes and slowdowns under heavy memory load, though accessing swap is slower than accessing RAM.


### 1. Checking Current Swap Space

To see if you already have swap space enabled and its size, use:

```bash
sudo swapon --show
```

### 2. Creating a Swap File

If you don't have swap space or need to increase it, you can create a swap file.

#### Step-by-Step Process:

1. **Create a swap file**:
   ```bash
   sudo fallocate -l 2G /swapfile
   ```
   Replace `2G` with the desired size of the swap file.

   If `fallocate` is not available, use `dd`:
   ```bash
   sudo dd if=/dev/zero of=/swapfile bs=1M count=2048
   ```

2. **Set the correct permissions**:
   ```bash
   sudo chmod 600 /swapfile
   ```

3. **Set up the swap space**:
   ```bash
   sudo mkswap /swapfile
   ```

4. **Enable the swap file**:
   ```bash
   sudo swapon /swapfile
   ```

5. **Verify the swap space**:
   ```bash
   sudo swapon --show
   ```

### 3. Adding Swap Partition

If you prefer using a swap partition instead of a swap file, you need to create and enable it.

#### Step-by-Step Process:

1. **Create a partition**: Use a partitioning tool like `fdisk` or `parted` to create a new partition and set its type to swap. For example, using `fdisk`:

   ```bash
   sudo fdisk /dev/sdX
   ```

   Within `fdisk`:
   - Type `n` to create a new partition.
   - Select the partition number and size.
   - Type `t` to change the partition type to `82` (Linux swap).
   - Type `w` to write changes.

2. **Set up the swap space**:
   ```bash
   sudo mkswap /dev/sdXn
   ```
   Replace `/dev/sdXn` with your actual swap partition (e.g., `/dev/sda3`).

3. **Enable the swap partition**:
   ```bash
   sudo swapon /dev/sdXn
   ```

4. **Verify the swap space**:
   ```bash
   sudo swapon --show
   ```

### 4. Making Swap Space Permanent

To ensure the swap space is used after a reboot, add it to `/etc/fstab`.

#### For Swap File:

1. Edit `/etc/fstab`:
   ```bash
   sudo nano /etc/fstab
   ```

2. Add the following line:
   ```plaintext
   /swapfile none swap sw 0 0
   ```

#### For Swap Partition:

1. Edit `/etc/fstab`:
   ```bash
   sudo nano /etc/fstab
   ```

2. Add a line like this, replacing `/dev/sdXn` with your partition:
   ```plaintext
   /dev/sdXn none swap sw 0 0
   ```

### 5. Adjusting Swappiness

The `swappiness` parameter controls how often the system uses swap space. A lower value means less frequent swapping.

1. **Check current swappiness**:
   ```bash
   cat /proc/sys/vm/swappiness
   ```

2. **Temporarily change swappiness**:
   ```bash
   sudo sysctl vm.swappiness=10
   ```

3. **Permanently change swappiness**:
   ```bash
   sudo nano /etc/sysctl.conf
   ```
   Add or modify the line:
   ```plaintext
   vm.swappiness=10
   ```

### Summary

By following these steps, you can effectively configure and manage swap space on your Linux system, whether by creating a swap file or a swap partition, ensuring it persists across reboots, and tuning its behavior to match your system's needs.