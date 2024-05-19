Managing physical storage partitions on a Linux system typically involves using tools such as `fdisk`, `parted`, `lsblk`, and `mkfs`. Below are the steps and commands to list, create, delete, and modify partitions on a disk.

>[!Partitions]
>Partitions are segments of a physical storage device, allowing it to be divided into isolated sections. Each partition can have its own file system and purpose, such as the operating system, user data, or swap space, enabling better organization and management of data.

### List Partitions

1. **Using `lsblk`**:
   ```bash
   lsblk
   ```

2. **Using `fdisk`**:
   ```bash
   sudo fdisk -l
   ```

3. **Using `parted`**:
   ```bash
   sudo parted -l
   ```

4. Example Output

```bash
$ lsblk /dev/sda /dev/sdb /dev/sr0
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda      8:0    0   100G  0 disk 
├─sda1   8:1    0    50G  0 part /
├─sda2   8:2    0    49G  0 part /home
└─sda3   8:3    0     1G  0 part [SWAP]
sdb      8:16   0   200G  0 disk 
└─sdb1   8:17   0   200G  0 part /mnt/data
sr0     11:0    1  1024M  0 rom  

```

#### Column Headers

- **NAME**: The name of the device or partition.
- **MAJ:MIN**: The major and minor device numbers.
- **RM**: Whether the device is removable (1) or not (0).
- **SIZE**: The size of the device or partition.
- **RO**: Whether the device is read-only (1) or not (0).
- **TYPE**: The type of the device (e.g., disk, part, rom).
- **MOUNTPOINT**: The mount point of the filesystem on the partition, if applicable.

### Create a Partition

1. **Using `fdisk`**:

   ```bash
   sudo fdisk /dev/sdX
   ```

   Replace `/dev/sdX` with the appropriate disk (e.g., `/dev/sda`). Then follow these steps within the `fdisk` prompt:
   - Type `n` to create a new partition.
   - Choose the partition type (`primary` or `logical`).
   - Specify the partition number and sector range.
   - Type `w` to write changes to the disk.

2. **Using `parted`**:

   ```bash
   sudo parted /dev/sdX
   ```

   Replace `/dev/sdX` with the appropriate disk. Within the `parted` prompt:
   - Type `mklabel gpt` to create a new GPT partition table (or `mklabel msdos` for MBR).
   - Type `mkpart` to create a new partition.
   - Specify the partition name, file system type, and start/end points.
   - Type `quit` to exit.

   Example:

   ```bash
   sudo parted /dev/sdX -- mklabel gpt
   sudo parted /dev/sdX -- mkpart primary ext4 0% 100%
   ```

### Delete a Partition

1. **Using `fdisk`**:

   ```bash
   sudo fdisk /dev/sdX
   ```

   Within the `fdisk` prompt:
   - Type `d` to delete a partition.
   - Select the partition number to delete.
   - Type `w` to write changes to the disk.

2. **Using `parted`**:

   ```bash
   sudo parted /dev/sdX
   ```

   Within the `parted` prompt:
   - Type `rm` followed by the partition number to delete it.
   - Type `quit` to exit.

   Example:

   ```bash
   sudo parted /dev/sdX -- rm 1
   ```

### Modify a Partition

1. **Resize a Partition with `parted`**:

   ```bash
   sudo parted /dev/sdX
   ```

   Within the `parted` prompt:
   - Type `resizepart` followed by the partition number and the new end point.
   - Type `quit` to exit.

   Example:

   ```bash
   sudo parted /dev/sdX -- resizepart 1 50GB
   ```

2. **Change Partition Type with `fdisk`**:

   ```bash
   sudo fdisk /dev/sdX
   ```

   Within the `fdisk` prompt:
   - Type `t` to change a partition type.
   - Select the partition number.
   - Enter the new hex code for the desired partition type.
   - Type `w` to write changes to the disk.

### Format a Partition

After creating or modifying partitions, you need to format them with a file system:

1. **Format with ext4**:

   ```bash
   sudo mkfs.ext4 /dev/sdX1
   ```

   Replace `/dev/sdX1` with the appropriate partition.

2. **Format with other file systems** (e.g., xfs, ntfs):

   ```bash
   sudo mkfs.xfs /dev/sdX1
   sudo mkfs.ntfs /dev/sdX1
   ```

### Example Workflow

1. **List existing partitions**:
   ```bash
   lsblk
   sudo fdisk -l
   ```

2. **Create a new partition**:
   ```bash
   sudo parted /dev/sdX -- mklabel gpt
   sudo parted /dev/sdX -- mkpart primary ext4 0% 50%
   ```

3. **Format the new partition**:
   ```bash
   sudo mkfs.ext4 /dev/sdX1
   ```

4. **Resize an existing partition**:
   ```bash
   sudo parted /dev/sdX -- resizepart 1 100GB
   ```

5. **Delete a partition**:
   ```bash
   sudo parted /dev/sdX -- rm 1
   ```

