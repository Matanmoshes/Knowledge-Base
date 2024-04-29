# `dd` Command
[dd]
The `dd` command in Unix and Linux is a low-level utility for converting and copying files, but it is particularly famed for its capability to perform direct copying from one storage device to another at the block level. This makes it especially powerful for tasks involving disk imaging, backups, and recovery.

### Basic Syntax

The basic syntax of `dd` is:

```bash
dd if=input_file of=output_file [options]
```

- **`if=`** specifies the input file (or device).
- **`of=`** specifies the output file (or device).
- **options** include several parameters that can be adjusted to control how `dd` operates.

### Key Options

- **`bs=`**: Sets both input and output block size. Typical values are `512` bytes (for traditional disk sectors), `4K` (for modern drives), and larger sizes for efficiency in certain situations.
- **`count=`**: Number of blocks to copy (combined with `bs`, determines total amount of data copied).
- **`skip=`**: Number of input blocks to skip before starting to copy (useful for offsetting into the input).
- **`seek=`**: Number of output blocks to skip before starting to write (useful for offsetting into the output).
- **`conv=`**: A set of options to convert the file as per the specified keywords, like `notrunc` (do not truncate the output file), `sync` (pad every input block to size of `bs` with nulls), `noerror` (continue after read errors), etc.

### Common Usage Examples

#### 1. **Creating Disk Images**
To create an image of a disk:

```bash
dd if=/dev/sda of=/path/to/disk_image.img bs=4M
```
This command will create an image (`disk_image.img`) of the `/dev/sda` device using a block size of 4 Megabytes, which can enhance the efficiency of the operation.

#### 2. **Restoring Disk Images**
To restore an image to a disk:

```bash
dd if=/path/to/disk_image.img of=/dev/sda bs=4M
```
This reverses the first example, writing the disk image back to a physical disk.

#### 3. **Creating Bootable USB Drives**
Copying an ISO image to a USB drive to make it bootable:

```bash
dd if=/path/to/linux.iso of=/dev/sdb bs=4M
```
This assumes that `/dev/sdb` is the USB drive. Care must be taken to verify the device name with tools like `lsblk` or `fdisk` before running the command to avoid overwriting the wrong disk.

#### 4. **Backup of a Partition**
To backup a single partition:

```bash
dd if=/dev/sda1 of=/path/to/partition_backup.img bs=4M
```
This command creates a backup image of the first partition on the disk `/dev/sda`.

### Safety and Precautions

- **Double-check device names**: `dd` can cause irreparable data loss if misused (commonly joked as "Disk Destroyer" due to its power and potential for damage).
- **Root permissions**: `dd` often requires root access, especially for reading/writing directly to disk devices.
- **Monitoring progress**: `dd` does not show progress in its traditional form. To view progress during the operation, you can use `status=progress` option in newer versions:

  ```bash
  dd if=/dev/sda of=/path/to/disk_image.img bs=4M status=progress
  ```

The `dd` command is extremely powerful and useful for a wide range of lower-level data manipulation tasks, especially those involving block devices. Always use it with care to ensure that you do not accidentally overwrite valuable data.