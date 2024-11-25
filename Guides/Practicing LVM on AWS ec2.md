# Practicing LVM: Setting Up a CentOS Machine in a Cloud Environment

To practice creating Physical Volumes (PVs) and Volume Groups (VGs) using Logical Volume Management (LVM) on a CentOS machine in a cloud environment, you'll need to:

1. **Set up a CentOS instance on a cloud platform.**
2. **Attach additional storage devices to the instance.**
3. **Install necessary packages and prepare the environment.**
4. **Practice LVM commands to create PVs and VGs.**

This guide will walk you through each step, providing detailed instructions to help you get started.

---

## Table of Contents

1. [Choosing a Cloud Provider](#1-choosing-a-cloud-provider)
2. [Setting Up a CentOS Instance](#2-setting-up-a-centos-instance)
3. [Attaching Additional Storage](#3-attaching-additional-storage)
4. [Connecting to Your Instance](#4-connecting-to-your-instance)
5. [Installing LVM2 Package](#5-installing-lvm2-package)
6. [Preparing the New Disks](#6-preparing-the-new-disks)
7. [Creating Physical Volumes](#7-creating-physical-volumes)
8. [Creating a Volume Group](#8-creating-a-volume-group)
9. [Practicing LVM Commands](#9-practicing-lvm-commands)
10. [Cleaning Up](#10-cleaning-up)
11. [Conclusion](#11-conclusion)

---

## 1. Choosing a Cloud Provider

Popular cloud providers include:

- **Amazon Web Services (AWS)**
- **Microsoft Azure**
- **Google Cloud Platform (GCP)**
- **DigitalOcean**

For this guide, we'll use **Amazon Web Services (AWS)** as an example, but the steps are similar for other providers.

---

## 2. Setting Up a CentOS Instance

### Step 1: Create an AWS Account

If you don't have one, [sign up for an AWS account](https://aws.amazon.com/free/).

### Step 2: Launch a CentOS Instance

1. **Navigate to the AWS Management Console** and go to the **EC2 Dashboard**.
2. **Click on "Launch Instance"**.
3. **Choose an Amazon Machine Image (AMI):**

   - Search for **CentOS** under **"Community AMIs"**.
   - Select a **CentOS 7** AMI (ensure it's from a trusted source).

4. **Choose an Instance Type:**

   - For practice purposes, a **t2.micro** (1 vCPU, 1 GiB RAM) is sufficient.

5. **Configure Instance Details:**

   - Default settings are acceptable for practice.
   - Ensure **Auto-assign Public IP** is enabled.

6. **Add Storage:**

   - The default size (8 GiB) is acceptable for the root volume.

7. **Add Tags (Optional):**

   - Add any tags to help identify your instance.

8. **Configure Security Group:**

   - Create a new security group allowing SSH access (port 22) from your IP address.

9. **Review and Launch:**

   - Click **"Launch"**.
   - When prompted, create a new key pair or use an existing one for SSH access.

---

## 3. Attaching Additional Storage

To practice LVM, you'll need additional storage devices.

### Step 1: Create New EBS Volumes

1. **Navigate to the EC2 Dashboard** and click on **"Volumes"** under **"Elastic Block Store"**.
2. **Click on "Create Volume"**.

   - **Volume Type:** General Purpose SSD (gp2) is sufficient.
   - **Size:** Choose a size (e.g., 1 GiB).
   - **Availability Zone:** Must match the zone of your EC2 instance (e.g., us-east-1a).

3. **Repeat** to create at least **two volumes** for practice.

### Step 2: Attach Volumes to the Instance

1. **Select a volume** from the list.
2. **Right-click** and choose **"Attach Volume"**.
3. **Select your EC2 instance** from the list.
4. **Repeat** for each volume.

---

## 4. Connecting to Your Instance

### Step 1: Locate Your Key Pair

Ensure you have the **.pem** file corresponding to your key pair.

### Step 2: Connect via SSH

On your local machine, open a terminal and run:

```bash
ssh -i /path/to/your-key.pem centos@ec2-instance-public-dns
```

- Replace `/path/to/your-key.pem` with the path to your key file.
- Replace `ec2-instance-public-dns` with your instance's public DNS name or IP address.

---

## 5. Installing LVM2 Package

CentOS typically comes with LVM2 installed. To verify and install if necessary:

```bash
# Check if LVM2 is installed
sudo rpm -qa | grep lvm2

# Install LVM2 if not present
sudo yum install -y lvm2
```

---

## 6. Preparing the New Disks

### Step 1: Identify Attached Volumes

List all block devices:

```bash
lsblk
```

Sample output:

```
NAME    MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
xvda    202:0    0  8G  0 disk
└─xvda1 202:1    0  8G  0 part /
xvdf    202:80   0  1G  0 disk
xvdg    202:96   0  1G  0 disk
```

- `xvda`: Root volume.
- `xvdf` and `xvdg`: Newly attached volumes.

### Step 2: Create Partitions (Optional)

You can use the entire disk or create partitions. To create a partition:

```bash
sudo fdisk /dev/xvdf
```

**fdisk Commands:**

- `n`: Create new partition.
- `p`: Primary partition.
- `1`: Partition number.
- Accept defaults for cylinder.
- `w`: Write changes.

Repeat for `/dev/xvdg` if desired.

### Step 3: Verify Partitions

```bash
lsblk
```

Sample output:

```
NAME    MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
xvda      ...       ...
└─xvda1   ...       ...
xvdf    202:80   0  1G  0 disk
└─xvdf1 202:81   0  1G  0 part
xvdg    202:96   0  1G  0 disk
└─xvdg1 202:97   0  1G  0 part
```

---

## 7. Creating Physical Volumes

Initialize the disks or partitions as physical volumes.

### Using Entire Disks:

```bash
sudo pvcreate /dev/xvdf /dev/xvdg
```

### Using Partitions:

```bash
sudo pvcreate /dev/xvdf1 /dev/xvdg1
```

Sample output:

```
Physical volume "/dev/xvdf1" successfully created.
Physical volume "/dev/xvdg1" successfully created.
```

---

## 8. Creating a Volume Group

Create a volume group from the physical volumes.

```bash
sudo vgcreate my_vg /dev/xvdf1 /dev/xvdg1
```

Sample output:

```
Volume group "my_vg" successfully created
```

---

## 9. Practicing LVM Commands

### Verify Physical Volumes

```bash
sudo pvs
```

Sample output:

```
PV         VG    Fmt  Attr PSize PFree
/dev/xvdf1 my_vg lvm2 a--  1.00g 1.00g
/dev/xvdg1 my_vg lvm2 a--  1.00g 1.00g
```

### Verify Volume Groups

```bash
sudo vgs
```

Sample output:

```
VG    #PV #LV #SN Attr   VSize VFree
my_vg   2   0   0 wz--n- 2.00g 2.00g
```

### Display Detailed Information

```bash
# Physical Volumes
sudo pvdisplay

# Volume Groups
sudo vgdisplay
```

### Create Logical Volumes (Optional)

To proceed further, you can create logical volumes:

```bash
sudo lvcreate -L 500M -n my_lv my_vg
```

### Verify Logical Volumes

```bash
sudo lvs
```

Sample output:

```
LV    VG    Attr       LSize Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
my_lv my_vg -wi-a----- 500.00m
```

---

## 10. Cleaning Up

If you wish to remove the configurations:

### Remove Logical Volumes

```bash
sudo lvremove /dev/my_vg/my_lv
```

### Remove Volume Group

```bash
sudo vgremove my_vg
```

### Remove Physical Volumes

```bash
sudo pvremove /dev/xvdf1 /dev/xvdg1
```

### Detach and Delete EBS Volumes (AWS Specific)

1. **Detach Volumes:**

   - In the AWS console, navigate to **"Volumes"**.
   - Select the volumes, right-click, and choose **"Detach Volume"**.

2. **Delete Volumes:**

   - After detaching, select the volumes again.
   - Right-click and choose **"Delete Volume"**.

---

## 11. Conclusion

You have now set up a CentOS machine in a cloud environment and practiced creating Physical Volumes and Volume Groups using LVM. This hands-on experience helps solidify your understanding of LVM components and commands.

---

**Additional Tips:**

- **Safety First:** Be cautious when working with disk management commands to avoid data loss.
- **Documentation:** Refer to the [Red Hat LVM documentation](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/logical_volume_manager_administration/index) for more in-depth information.
- **Practice:** Try adding more disks, creating different volume groups, and experimenting with other LVM features like resizing and snapshots.

---

