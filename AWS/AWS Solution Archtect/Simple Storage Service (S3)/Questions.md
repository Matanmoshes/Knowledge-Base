

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