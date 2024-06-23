
### AWS EC2 Volumes and Snapshots

### Volumes

- **Definition:** Virtual hard disks that exist on EBS (Elastic Block Store).
- **Usage:** Each EC2 instance requires a minimum of one volume, known as the root device volume, where the operating system (OS) is installed (e.g., Windows, Linux).

### Snapshots

- **Definition:** Point-in-time copies of EBS volumes stored on S3.
- **Incremental Nature:** Only the data that has changed since the last snapshot is stored, making snapshots efficient in terms of space and time.

### Key Points about Snapshots

1. **Consistency:** To ensure consistent snapshots, it’s recommended to stop the instance before taking a snapshot to ensure all data is written to the disk.
2. **Encryption:** Snapshots of encrypted volumes will also be encrypted.
3. **Sharing:** Snapshots can be shared within the same region or copied to another region for sharing.

### EBS Volumes

- **Location:** Always in the same availability zone as the EC2 instance to avoid latency issues.
- **Scalability:** You can resize volumes on the fly without downtime. However, you will need to extend the file system in the OS to use the new space.
- **Volume Types:** Can be changed on the fly (e.g., from GP2 to IO2) without needing to stop the instance.


### Demo Steps

1. **Provision an Instance:**
    - Launch an EC2 instance and specify the root device volume.
    - Add additional volumes if needed.
    - Set options like encryption and deletion on termination.
2. **Modify Volumes:**
    - Modify volume types and sizes on the fly through the AWS console.
    - Detach and delete volumes as needed.
3. **Snapshots:**
    - Create snapshots of EBS volumes.
    - Copy snapshots to other regions to facilitate EC2 instance migration.
4. **Launch Instances from Snapshots:**
    - Use snapshots to create AMIs (Amazon Machine Images).
    - Launch new instances from these AMIs in different regions.

### Exam Tips

1. **Volumes and Snapshots Locations:**
    - Volumes exist on EBS.
    - Snapshots exist on S3.
2. **Snapshots Characteristics:**
    - Snapshots are incremental and take point-in-time copies of volumes.
    - The first snapshot takes longer as it copies all data.
    - For consistent snapshots, stop the instance before taking the snapshot.
3. **Snapshot Sharing:**
    - Snapshots can be shared within regions or copied to other regions for sharing.
4. **Resizing Volumes:**
    - Volumes can be resized and their types changed on the fly without causing downtime.
5. **Migrating Instances:**
    - To migrate instances across regions:
        1. Create a snapshot of the root volume.
        2. Copy the snapshot to the target region.
        3. Create an image from the snapshot in the target region.
        4. Launch an instance from the image.

This summary provides a comprehensive overview of EBS volumes and snapshots, their characteristics, usage, and key points to remember for exams.