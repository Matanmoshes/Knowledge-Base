# Amazon Elastic File System (EFS) Guide

## Introduction

Amazon Elastic File System (Amazon EFS) provides a simple, serverless, and elastic file system that allows sharing file data without the need to provision or manage storage. This guide will walk you through modifying three existing EC2 instances to use a shared EFS storage volume instead of duplicated Elastic Block Store (EBS) volumes, reducing costs by storing data in one location. By the end of this guide, you will understand how to create EFS volumes and attach them to an EC2 instance.

## Structure

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/ae5789d5-3051-420d-8416-d817f388473e/91ed2b00-bff0-49ef-9509-6e293c6c52f7/Untitled.png)

## Steps to Follow

### 1. Log in to AWS Management Console

- Log in to the AWS Management Console using the credentials provided in the lab instructions.
- Ensure you are using the `us-east-1` region.

### 2. Review Your Resources

- Navigate to EC2 using the Services menu or the unified search bar.
- In the Resources section, select `Instances (running)`.
- Check the instance details for `webserver-01` and note the 10 GiB disk attached.
- This configuration is the same for `webserver-02` and `webserver-03`.

### 3. Create an EFS File System

### Navigate to EFS

- Open a new browser tab and navigate to EFS.
- Click `Create file system`.

### Fill in the Details

- **Name:** Enter `SharedWeb`.
- **Virtual Private Cloud (VPC):** Select the provided VPC.
- Click `Customize`.
- **File system type:** Select `One Zone`.
- **Availability Zone:** Leave `us-east-1a` selected.
- **Lifecycle management:** Ensure `None` is selected under `Transition into Archive`.
- Click `Next > Next > Next > Create`.

### 4. Configure the Security Groups

### Update Security Groups

- Go back to the EC2 browser tab.
- Select `Security Groups` from the sidebar menu.
- Click the checkbox next to the non-default security group to show details.
- Select the `Inbound rules` tab and click `Edit inbound rules`.
- **Add rule:**
    - **Type:** Select `NFS`.
    - **Source:** Enter `0.0.0.0/0`.
- Click `Save rules`.

### 5. Mount the EFS File System

### Connect to EC2 Instance

- In the EC2 dashboard, select `Instances (running)`.
- With `webserver-01` selected, click `Connect`.
- This opens a terminal for your EC2 instance in a new browser tab.

### Mount the File System

- In the terminal, list your block devices: `lsblk`.
- View the data inside the 10 GiB disk mounted to `/data`: `ls /data`.
- Create a directory to attach your EFS volume: `sudo mkdir /efs`.

### Attach EFS Volume

- Go back to the EFS tab and click `Attach` for the `SharedWeb` file system.
    
- Select `Mount via IP` and copy the NFS command provided.
    
- In the terminal, paste the command and modify the mount point:
    
    ```bash
    sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport [Your EFS Volume IP Here]:/ /efs
    ```
    
- Press `Enter` to run the command.
    

### 6. Test the File System

- View the newly mounted EFS volume: `ls /efs`.
- List the block devices again: `lsblk`.
- View the mounts: `mount`.
- Check the file system mounts: `df -h`.

### 7. Move Data to EFS

- Move all files from `/data` to the `/efs` file system:
    
    ```bash
    sudo rsync -rav /data/* /efs
    ```
    
- View the files now in the `/efs` file system: `ls /efs`.
    

### 8. Remove Old Data

### From `webserver-01`

- Unmount the partition: `sudo umount /data`.
- Edit the `/etc/fstab` file: `sudo nano /etc/fstab`.
- Remove the UUID line and add the new mount point entry:

```
<EFS MOUNT IP>:/ /data nfs4 <OPTIONS> 0 0

```

- Save and exit Nano (Ctrl+X, Y, Enter).
- Unmount `/efs`: `sudo umount /efs`.
- View file systems: `df -h`.
- Mount all: `sudo mount -a`.
- Confirm the NFS share is mounted: `df -h`.

### Detach and Delete EBS Volume

- Navigate back to the EC2 tab and select `Volumes`.
- Detach the 10 GiB volume from `webserver-01` and delete it.

### Repeat for `webserver-02` and `webserver-03`

- Follow the same steps for `webserver-02` and `webserver-03` to mount the EFS and remove old data.

## Conclusion

By following this guide, you will successfully modify your EC2 instances to use a shared EFS storage volume, reducing costs and simplifying storage management.