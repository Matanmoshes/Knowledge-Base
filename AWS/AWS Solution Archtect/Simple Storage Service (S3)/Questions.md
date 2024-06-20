

**Question: Versioning on your bucket was recently suspended and, after this, your boss deleted an object whose version ID is null and wants to restore it. What do you do?**

1. Suspended or not, versioning doesn't protect against deletions. The object is gone forever.
2. Re-enable versioning and remove the deletion marker to restore the object.
3. Look in the bucket named "s3-backups". The object will be in there and can be restored to the original bucket.
4. It's not possible since versioning was suspended. The object is gone.


**"It's not possible since versioning was suspended. The object is gone."**

### Explanation:

1. **Versioning-Suspended Buckets**:
   - When versioning is suspended, objects that are created or modified after suspension will have a `null` version ID.
   - Deleting an object with a `null` version ID in a versioning-suspended bucket will replace it with a delete marker that also has a `null` version ID.
   - If an object with a `null` version ID is deleted, its content is permanently lost and replaced by a delete marker with a `null` version ID. There is no previous version to restore from.

2. **DELETE Request Behavior**:
   - In a versioning-suspended bucket, a DELETE request will:
     - Remove an object whose version ID is `null`.
     - Insert a delete marker if there is no `null` version of the object in the bucket.
     - Permanently delete a specified version if the version ID is included in the DELETE request.

3. **Restoration Possibility**:
   - If the object that was deleted had a `null` version ID and versioning was suspended, there is no previous version to revert to because the object with a `null` version ID is the only version. Once deleted, the content is permanently lost.

### Conclusion:
Given that the object with a `null` version ID was deleted while versioning was suspended, it is not possible to restore the object. The correct answer reflects the permanent loss of the object:

**"It's not possible since versioning was suspended. The object is gone."**

---



**Question: Which of the following is NOT true?**

1. **S3 lifecycle policies help automate the handling of objects in S3 buckets throughout their entire existence.**
2. **Lifecycle policies can define transition actions, which specify when objects transition to another storage class.**
3. **You can create an S3 lifecycle policy to migrate objects from Glacier to Standard-IA.**
4. **Lifecycle policies can define expiration actions, which specify when objects expire.**

**Correct Answer: 3. You can create an S3 lifecycle policy to migrate objects from Glacier to Standard-IA.**

**Explanation:**

- **Option 1**: True. S3 lifecycle policies do help automate the handling of objects in S3 buckets throughout their entire existence by defining actions such as transitioning to different storage classes or expiring objects.

- **Option 2**: True. Lifecycle policies can define transition actions, specifying when objects transition to another storage class, such as from S3 Standard to S3 Standard-IA or from S3 Standard-IA to Glacier.

- **Option 3**: **Not True**. The AWS documentation states that it is not possible to transition objects from Glacier (or Glacier Deep Archive) back to any other storage class using lifecycle policies. To move an object out of Glacier, you must restore a temporary copy and then use a copy operation to place it in the desired storage class.

- **Option 4**: True. Lifecycle policies can define expiration actions, which specify when objects should expire and be deleted automatically.

### Conclusion:
Based on the documentation and the detailed explanation, the correct answer is:
**"You can create an S3 lifecycle policy to migrate objects from Glacier to Standard-IA."** This statement is not true.

---

**Question: Which S3 Glacier storage class features a standard object retrieval time of 3-5 hours with expedited retrievals typically in 1-5 minutes?**

1. S3 Glacier Instant Retrieval
2. S3 Glacier Flexible Retrieval
3. S3 Intelligent-Tiering
4. Amazon S3 Glacier Deep Archive


**Correct answer: S3 Glacier Flexible Retrieval**

### Explanation:

- **S3 Glacier Instant Retrieval**: This class is designed for long-term archive data that needs to be retrieved quickly. It offers instant retrieval times.
  
- **S3 Glacier Flexible Retrieval**: Previously known as just S3 Glacier, this class provides flexible retrieval options with standard retrieval times of 3-5 hours and expedited retrievals typically in 1-5 minutes.
  
- **S3 Intelligent-Tiering**: This class automatically moves data between two access tiers when access patterns change, and it's not specifically designed for archival purposes.
  
- **Amazon S3 Glacier Deep Archive**: This class is the lowest-cost storage option designed for long-term archiving, with retrieval times typically taking 12 hours or more for standard retrieval and up to 48 hours for bulk retrieval.

So the correct answer is:
**S3 Glacier Flexible Retrieval**


---

### Question

How does availability and durability differ in S3?

1. Availability is the ability to access your data, while durability is the ability of AWS to ensure your data is properly stored in S3.
2. Durability refers to how many times your data can be overwritten, while availability is the ability to download the data after one of these overwrites.
3. Availability and durability are the same thing; they both define how long AWS will store your data.
4. Availability is how much data you can download, while durability is how much local storage space your computer has available to receive those files.

### Answer

**Availability is the ability to access your data, while durability is the ability of AWS to ensure your data is properly stored in S3.**

### Explanation

In Amazon S3:

- **Availability** refers to the percentage of time that S3 is accessible and able to serve requests to read and write data. AWS aims to ensure that you can access your data when needed, with S3 providing 99.99% availability.
  
- **Durability** refers to the reliability of the data storage over time, specifically how likely it is that your data will be intact and uncorrupted. Amazon S3 is designed for 99.999999999% (11 nines) durability, meaning your data is highly secure against loss or corruption.

These two concepts are fundamental to understanding the robustness of data storage services. Availability ensures access to data, while durability ensures the integrity and longevity of the data stored.

---

### Question

If you have data that needs to be instantly retrievable and needs to be accessed only once per month, which S3 storage class would you select?

1. S3 Glacier Deep Archive
2. S3 Standard-IA
3. S3 Standard
4. S3 Glacier Flexible Retrieval (formerly known as S3 Glacier)

### Answer

**S3 Standard-IA**

### Explanation

S3 Standard-IA (Infrequent Access) is designed for data that is accessed less frequently but requires rapid access when needed. It provides lower storage costs compared to the S3 Standard storage class, with slightly higher retrieval costs. This makes it ideal for data that needs to be instantly retrievable but is accessed infrequently, such as data accessed only once per month. 

The other options are not suitable for this use case:
- **S3 Glacier Deep Archive**: Designed for long-term archiving with retrieval times in hours, not suitable for instant access.
- **S3 Standard**: Provides instant access but is more costly for infrequently accessed data.
- **S3 Glacier Flexible Retrieval**: Also designed for archival storage with retrieval times ranging from minutes to hours, not suitable for instant access.

---

### Question

Which of the following statements is true?

1. You can increase your Amazon S3 read or write performance by creating more prefixes in a bucket and parallelizing reads/writes.
2. Amazon S3 allows cross-account access by default.
3. The bucket owner is the owner of an S3 object, regardless of the identity of the person who uploaded it.
4. Amazon S3 does not encrypt data uploads.

### Answer

**You can increase your Amazon S3 read or write performance by creating more prefixes in a bucket and parallelizing reads/writes.**

### Explanation

Amazon S3 performance can be improved by using multiple prefixes in a bucket. Each prefix can handle its own read and write operations, and by parallelizing these operations across multiple prefixes, you can achieve higher throughput. This technique helps distribute the load and avoid bottlenecks associated with a single prefix.

The other statements are not true:
- **Amazon S3 allows cross-account access by default**: Cross-account access is not allowed by default and must be explicitly configured using bucket policies, IAM policies, or resource-based policies.
- **The bucket owner is the owner of an S3 object, regardless of the identity of the person who uploaded it**: By default, the uploader of an object is the owner of the object, even if they do not own the bucket. Ownership can be managed through permissions and policies.
- **Amazon S3 does not encrypt data uploads**: Amazon S3 offers multiple encryption options for data at rest, including server-side encryption (SSE) and client-side encryption.

### Example

In Amazon S3, a prefix is essentially the "path" or "folder" structure within a bucket that you use to organize and manage your objects. Although Amazon S3 is a flat storage system without a true hierarchical file structure, you can simulate a folder-like organization by using prefixes.

For example, consider the following S3 object keys:

- `images/2024/june/sunrise.jpg`
- `images/2024/june/sunset.jpg`
- `documents/reports/annual_report.pdf`
- `videos/tutorials/aws_s3.mp4`

In these examples:
- `images/2024/june/` is a prefix for the `sunrise.jpg` and `sunset.jpg` objects.
- `documents/reports/` is a prefix for the `annual_report.pdf` object.
- `videos/tutorials/` is a prefix for the `aws_s3.mp4` object.

---

### Question

What kind of data should be stored in S3 Standard?

1. Data that is frequently used
2. Data that is infrequently used
3. Data that is a secondary copy of a primary source
4. Data that is rarely used

### Answer

**Data that is frequently used**

### Explanation

Amazon S3 Standard storage class is designed for data that is accessed frequently. It provides high durability, availability, and performance for frequently accessed data. This storage class is ideal for use cases such as:

- Content distribution
- Big data analytics
- Mobile and gaming applications
- Active data archives

For data that is infrequently used, AWS offers other storage classes like S3 Standard-IA (Infrequent Access) and S3 One Zone-IA. For rarely used data, you might consider S3 Glacier or S3 Glacier Deep Archive. For secondary copies of primary data, S3 Standard can be used, but often these types of data might be better suited to infrequent access or archival storage classes to optimize costs.


---

### Question

What is the largest object you can store in S3?

1. 50 GB
2. 1 TB
3. Unlimited
4. 5 TB

### Answer

**5 TB**

### Explanation

Amazon S3 supports storing individual objects up to 5 terabytes in size. While there is no limit to the number of objects you can store in an S3 bucket, each individual object has a maximum size limit of 5 TB. If you need to store larger data, you would need to split it into multiple objects.