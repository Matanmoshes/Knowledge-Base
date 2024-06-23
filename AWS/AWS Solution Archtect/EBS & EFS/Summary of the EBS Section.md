# Summary of the EBS Section

### EBS Volumes

- **SSD Volumes**: Highly available and scalable storage volumes for EC2 instances.
    - **gp2 (General Purpose SSD)**: Suitable for boot disks and general applications. Up to 16,000 IOPS per volume, 99.9% (3 nines) durability.
    - **gp3**: Suitable for high-performance applications. Predictable 3,000 IOPS baseline performance and 125 MB/s throughput, regardless of volume size. 99.9% (3 nines) durability.
    - **io1 (Provisioned IOPS SSD)**: Suitable for online transaction processing and latency-sensitive applications. Up to 50 IOPS per GiB and 64,000 IOPS per volume. High performance, most expensive, 99.9% (3 nines) durability.
    - **io2**: Latest generation provisioned IOPS SSD. Suitable for the same applications as io1. Up to 500 IOPS per GiB and 64,000 IOPS per volume. 99.999% (5 nines) durability.
- **Magnetic Storage**:
    - **Throughput Optimized HDD (st1)**: Suitable for big data, data warehouses, ETL. Max throughput of 500 MB/s. Cannot be a boot volume, 99.9% (3 nines) durability.
    - **Cold HDD (sc1)**: Suitable for less frequently accessed data. Max throughput of 250 MB/s. Lowest cost, cannot be a boot volume, 99.9% (3 nines) durability.

### Volumes and Snapshots

- **Volumes** exist on EBS.
- **Snapshots** exist on S3 and are point-in-time photographs of volumes, incremental in nature.
- For consistent snapshots, stop the instance and detach the volume.
- Snapshots can be shared between AWS accounts and regions (by copying the snapshot).
- EBS volumes can be resized and their types changed on the fly.

### AMIs: EBS vs. Instance Store

- **Instance Store Volumes (Ephemeral Storage)**:
    - Cannot be stopped. If the underlying host fails, data is lost.
    - Can be rebooted without data loss.
    - Deleted upon instance termination.
- **EBS Volumes**:
    - Can be stopped without data loss.
    - Can be rebooted without data loss.
    - By default, root volumes are deleted upon termination, but this can be changed.
- **AMI**: A blueprint for an EC2 instance (includes architecture, OS, instance type, etc.).

### Encrypted Volumes

- Data-at-rest and data-in-flight are encrypted.
- Snapshots and volumes created from encrypted snapshots are encrypted.
- To encrypt an existing unencrypted root device volume:
    - Create a snapshot of the unencrypted volume.
    - Create a copy of the snapshot and select the encrypt option.
    - Create an AMI from the encrypted snapshot.
    - Launch a new encrypted instance from the AMI.

### EC2 Hibernation

- Preserves in-memory RAM on persistent storage (EBS).
- Faster boot-up as the OS does not need to reload.
- RAM must be less than 150 GB.
- Supported for C, M, and R class families, and available for Windows, Amazon Linux 2 AMI, and Ubuntu.
- Instances cannot be hibernated for more than 60 days.
- Available for on-demand and reserved instances.

### EFS (Elastic File System)

- Supports NFSv4 protocol.
- Scalable, elastic, and can support thousands of concurrent NFS connections.
- Only pay for what you use.
- Data is stored across multiple AZs within a region.
- Provides read-after-write consistency.
- Use EFS for highly scalable shared storage for Linux instances.

### FSx (Windows and Lustre)

- **FSx for Windows**: Centralized storage for Windows-based applications (e.g., SharePoint, SQL Server). Supports SMB-based file services.
- **FSx for Lustre**: High-performance storage for compute-intensive workloads (e.g., HPC, machine learning). Suitable for applications needing high-speed, high-capacity distributed storage.

### AWS Backup

- Centralized backup for multiple AWS services (EC2, EBS, EFS, FSx, Storage Gateway, RDS, DynamoDB).
- Integrates with AWS Organizations for cross-account backup management.
- **Benefits**:
    - **Central Management**: Single console for managing backups.
    - **Automation**: Automated backup schedules, retention policies, lifecycle policies.
    - **Compliance**: Enforce backup policies, encryption, and auditing.

By understanding these key points, you will be well-prepared for the AWS Solutions Architect Associate exam regarding EBS and related services.