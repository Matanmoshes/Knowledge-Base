### Question

You've been tasked with creating a file system for a Linux workload that should support concurrent access to the same file or directory from thousands of compute instances and handle massive datasets, up to hundreds of gigabytes per second. What AWS service would you use?

1. Amazon FSx for Lustre
2. Amazon EBS Provisioned IOPS
3. Amazon EFS High Performance
4. Amazon FSx

### Answer

**Amazon FSx for Lustre**

### Explanation

Amazon FSx for Lustre is specifically designed for high-performance computing (HPC) and machine learning workloads that require fast, scalable, and concurrent access to large datasets. It provides a parallel file system that supports concurrent access from thousands of compute instances and can handle massive datasets with throughput scaling to hundreds of gigabytes per second. This makes it an ideal choice for workloads that need to process large amounts of data quickly and efficiently.

- **Amazon EBS Provisioned IOPS**: This is designed for high-performance storage at the block level for a single instance, not for concurrent access from multiple instances.
- **Amazon EFS High Performance**: While Amazon EFS supports concurrent access from multiple instances and is suitable for many use cases, it is not optimized for the extremely high throughput required for HPC and similar workloads.
- **Amazon FSx**: This is a general term that includes multiple FSx offerings (e.g., FSx for Windows File Server, FSx for Lustre). The specific service suitable for this use case is Amazon FSx for Lustre.

[Amazon FSx for Lustre FAQs Page- Amazon Web Services](https://aws.amazon.com/fsx/lustre/faqs)

---

### Question

Which of the following statements about EC2 hibernation is true?

1. EC2 hibernation can result in slower boot processes since you must reload the operating system.
2. Processes that were previously running on the instance must be restarted.
3. The instance gains a new instance ID.
4. Only certain types of EC2 instances support hibernation.

### Answer

**Only certain types of EC2 instances support hibernation.**

### Explanation

EC2 hibernation allows you to pause and resume your instances. When an instance is hibernated, its state (including the contents of its memory, processes, and operating system) is saved to the root EBS volume. When the instance is resumed, it is restored to its previous state, so processes do not need to be restarted, and the instance retains its instance ID. However, not all instance types and configurations support hibernation; it is available only for certain types of EC2 instances, and there are specific requirements and limitations associated with its use.

[Hibernate your Amazon EC2 instance - Amazon Elastic Compute Cloud](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Hibernate.html)

---

### Question

Which of the following statements about the Amazon FSx services is false?

1. Amazon FSx for Windows File Server encrypts data via self managed KMS keys.
2. Amazon FSx for Windows File Server supports the Server Message Block (SMB) protocol.
3. Amazon FSx for Lustre works best for workloads requiring fast storage, such as machine learning.
4. Amazon FSx for Lustre can't store data directly on Amazon S3.

### Answer

**Amazon FSx for Lustre can't store data directly on Amazon S3.**

### Explanation

This statement is false. Amazon FSx for Lustre is designed to integrate closely with Amazon S3. It allows you to link your Lustre file system to an S3 bucket, enabling fast and seamless access to S3 data. You can read data directly from S3 into your Lustre file system and write data back to S3, making it ideal for workloads that require high-speed data processing and integration with S3 storage.

The other statements are true:

- Amazon FSx for Windows File Server encrypts data using AWS Key Management Service (KMS) keys, which can be customer-managed.
- Amazon FSx for Windows File Server supports the SMB protocol, allowing compatibility with a wide range of applications and services.
- Amazon FSx for Lustre is optimized for high-performance workloads, such as machine learning, that require fast storage and retrieval of large datasets.

[Lustre File System - Amazon FSx for Lustre - AWS](https://aws.amazon.com/fsx/lustre/)

---

### Question

Which of the following statements about EBS volumes (in the context of EBS-backed EC2 instances) is false?

1. At least one EBS volume is required for each EC2 instance.
2. EBS volumes must be in the same AZ as the EC2 instance attached to them.
3. EBS volumes are encrypted by default.
4. EBS volume types and sizes can be altered without starting or stopping the instance to which they are attached.

### Answer

**EBS volumes are encrypted by default.**

### Explanation

EBS volumes are not encrypted by default; encryption must be explicitly enabled when the volume is created. However, you can set account settings to automatically encrypt new EBS volumes in some regions.

The other statements are true:

- At least one EBS volume (the root volume) is required for each EC2 instance.
- EBS volumes must be in the same Availability Zone (AZ) as the EC2 instance they are attached to.
- EBS volume types and sizes can indeed be altered without stopping the instance to which they are attached. You can modify the volume size, performance characteristics, and type dynamically.

[Amazon EBS encryption - Amazon EBS](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSEncryption.html)

---

### Question

How do you add more storage to EFS?

1. Create a new, larger EBS volume and designate it as shared EFS storage in the configuration page.
2. No action is needed, as AWS will automatically size EFS based on your usage.
3. Execute the 'expand_efs_size' API call and specify your new storage size.
4. Create a new, larger EFS volume to swap with your smaller one.

### Answer

**No action is needed, as AWS will automatically size EFS based on your usage.**

### Explanation

Amazon Elastic File System (EFS) is designed to be an elastic file system, meaning it automatically grows and shrinks as you add and remove files. You don't need to provision storage space in advance or perform any manual operations to increase the storage size. The service automatically scales based on the amount of data stored, providing virtually unlimited storage capacity without the need for manual intervention.

---

### Question

You just took a snapshot of an EBS volume. Where is it stored?

1. EFS
2. EC2
3. S3
4. Data Backup Service

### Answer

**S3**

### Explanation

When you take a snapshot of an EBS volume, it is stored in Amazon S3. Although the snapshots are stored in S3, they are managed by the Amazon EBS service and are not directly accessible via the S3 interface. The snapshots are used to create new EBS volumes or to restore existing volumes to a previous state.

[Amazon EBS snapshots - Amazon EBS](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSSnapshots.html)

---

Which of the following statements about EBS volume encryption is false?

Unencrypted EBS volume snapshots allow encryption. You can create an unencrypted EBS volume from an encrypted snapshot by unselecting the "encryption" check box when restoring it. EBS volumes that are encrypted offer end-to-end encryption. EBS volumes are not encrypted by default.

[How do I unencrypt an encrypted EBS volume?](https://aws.amazon.com/premiumsupport/knowledge-center/create-unencrypted-volume-cmk/)

---

### Question

What type of storage does EFS offer?

1. System
2. Object
3. File
4. Block

### Answer

**File**

### Explanation

Amazon Elastic File System (EFS) provides file storage. EFS is a scalable, fully managed file storage service that can be mounted by multiple EC2 instances concurrently, allowing them to access the file system just like a traditional file system. This makes it suitable for a wide range of use cases that require a shared file system with standard file system semantics.

---

### Question

Which of the following statements about Amazon Machine Images (AMIs) is true?

1. AMIs cannot be copied to other regions.
2. The root storage device of each AMI can be altered for an EBS volume or an instance store.
3. AMIs backed by EBS use ephemeral storage.
4. Amazon Machine Images are region specific.

### Answer

**Amazon Machine Images are region specific.**

### Explanation

Amazon Machine Images (AMIs) are region-specific, meaning they are available for use only within the region they were created. However, you can copy an AMI to other regions if needed, which allows you to deploy instances based on that AMI in different regions.

The other statements are not true:

- **AMIs cannot be copied to other regions**: AMIs can indeed be copied to other regions.
- **The root storage device of each AMI can be altered for an EBS volume or an instance store**: The root storage device is fixed for a given AMI; it is either backed by an EBS volume or an instance store, but you cannot alter it once the AMI is created.
- **AMIs backed by EBS use ephemeral storage**: AMIs backed by EBS use EBS volumes, which are persistent storage, not ephemeral. Instances backed by instance store use ephemeral storage.

[Amazon Machine Images (AMI) - Amazon Elastic Compute Cloud](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AMIs.html)

---

### Question

What is the best way to ensure the security of both data-at-rest and data-in-transit between an instance and its attached EBS storage?

1. Revoke IAM credentials that would allow users to read the data using the EBS service.
2. Do nothing. EBS volumes are protected by default.
3. Encrypt the EBS volume.
4. Copy your EBS volumes to the AWS Secure Region.

### Answer

**Encrypt the EBS volume.**

### Explanation

Encrypting the EBS volume ensures that both data-at-rest and data-in-transit between the instance and the EBS storage are secure. AWS EBS encryption uses AWS Key Management Service (KMS) keys to encrypt the data. This encryption happens transparently and includes encryption of data at rest, data moving between the EBS volume and the instance, snapshots, and volume backups.

The other options do not provide comprehensive security for both data-at-rest and data-in-transit:

- **Revoke IAM credentials**: This controls access but does not encrypt the data.
- **Do nothing**: EBS volumes are not encrypted by default.
- **Copy your EBS volumes to the AWS Secure Region**: This is not a standard or effective practice for ensuring data security and does not address in-transit security.

[Amazon EBS encryption - Amazon EBS](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSEncryption.html)