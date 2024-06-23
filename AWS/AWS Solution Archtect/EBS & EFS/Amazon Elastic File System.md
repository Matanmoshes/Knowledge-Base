### Amazon Elastic File System (EFS) Overview

### What is EFS?

- **Definition:** Amazon Elastic File System (EFS) is a managed Network File System (NFS) that can be mounted on multiple EC2 instances simultaneously.
- **Availability:** Works across multiple Availability Zones, providing centralized storage.
- **Characteristics:** Highly available, scalable, and expensive.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/ae5789d5-3051-420d-8416-d817f388473e/ff301be6-d9d8-4412-9dab-96bb83c33c0c/Untitled.png)

### Use Cases for EFS

- **Content Management Systems:** Ideal for sharing content across multiple EC2 instances (e.g., WordPress, Joomla).
- **Web Server Farms:** Suitable for maintaining a single folder structure accessible by numerous web servers.
- **Shared Database Access:** Allows multiple instances to access the same data concurrently.

### EFS Features

- **NFSv4 Protocol:** EFS uses the Network File System Version 4 protocol.
- **Compatibility:** Works with Linux-based AMIs; Windows is not supported.
- **Encryption:** Uses encryption at rest with AWS Key Management Service (KMS).
- **Scalability:** Automatically scales based on the amount of data stored, eliminating the need for capacity planning.
- **Performance:** Offers high throughput (up to 10 gigabits per second) and can handle thousands of concurrent connections.
- **Storage Capacity:** Scales to petabytes.

### Performance Options

- **General Purpose:** Suitable for web servers and content management systems.
- **Max I/O:** Designed for big data and media processing applications.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/ae5789d5-3051-420d-8416-d817f388473e/95b8e134-a973-40cb-9f0d-c98d7c98f3cf/Untitled.png)

### Storage Tiers

- **Standard Storage Tier:** For frequently accessed files.
- **Infrequent Access Storage Tier:** For less frequently accessed files, reducing costs.

### Setting Up EFS in AWS Console (Demo)

1. **Navigate to EFS:** Access EFS through the AWS console.
2. **Create a File System:** Click "Create File System," name it (e.g., MyEFS), select the default VPC, and create it.
3. **Configuration:** Monitor size, enable backups, manage lifecycle policies, and adjust performance settings as needed.

### Exam Tips

- **Key Points to Remember:**
    - **NFSv4 Protocol:** EFS uses the NFSv4 protocol.
    - **Pay for Usage:** Charges are based on the storage used, with no need for pre-provisioning.
    - **Scalability:** Can scale up to petabytes.
    - **Concurrent Connections:** Supports thousands of concurrent NFS connections.
    - **Availability Zones:** Data is stored across multiple Availability Zones within a region.
    - **Consistency:** Provides Read-after-write consistency.
    - **Use Case Scenarios:** For highly scalable shared storage using NFS, EFS is the go-to option.

By understanding these key features and use cases, you'll be well-prepared to answer questions about EFS on the AWS Solutions Architect Associate exam.

---

# Reference:

[Amazon EFS performance - Amazon Elastic File System](https://docs.aws.amazon.com/efs/latest/ug/performance.html)

[Amazon Elastic File System (EFS) | Cloud File Storage | FAQs](https://aws.amazon.com/efs/faq/)