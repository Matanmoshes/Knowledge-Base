### AWS EBS Encryption

### What is EBS Encryption?

- **Definition:** EBS Encryption allows you to encrypt your EBS volumes using data keys.
- **Algorithm:** Uses industry-standard AES-256 encryption.
- **Key Management:** Utilizes AWS Key Management Service (KMS) and customer master keys (CMKs).

### What Happens When You Encrypt?

- **Data at Rest:** Data stored in the volume is encrypted.
- **Data in Transit:** Data moving between the instance and the volume is encrypted.
- **Snapshots:** All snapshots created from the encrypted volumes are also encrypted.
- **New Volumes:** Volumes created from encrypted snapshots are automatically encrypted.

### Key Facts about EBS Encryption

1. **Transparent Handling:** Encryption and decryption are managed transparently by AWS.
2. **Minimal Impact:** There is minimal impact on latency and performance.
3. **Copying Snapshots:** Copying an unencrypted snapshot allows you to encrypt it.
4. **Encrypted Snapshots:** Snapshots of encrypted volumes are automatically encrypted.
5. **Root Device Volumes:** You can now encrypt root device volumes upon creation.

### Steps to Encrypt an Unencrypted Volume

1. **Create a Snapshot:** Take a snapshot of the unencrypted root device volume.
2. **Copy the Snapshot:** Create a copy of the snapshot and select the "Encrypt" option.
3. **Create an AMI:** Create an AMI from the encrypted snapshot.
4. **Launch a New Instance:** Use the AMI to launch a new instance that will have the encrypted volume.

### Demo Steps

1. **Identify the Volume:** Select the unencrypted volume.
2. **Create a Snapshot:** Create a snapshot of the volume.
3. **Copy the Snapshot:** Use the "Copy Snapshot" action to create an encrypted copy of the snapshot.
4. **Create an Image:** Create an AMI from the encrypted snapshot.
5. **Launch an Instance:** Launch a new instance from the AMI to ensure the root device volume is encrypted.

### Exam Tips

1. **Encrypted Volumes:**
    - Data at rest is encrypted.
    - Data in transit between the instance and the volume is encrypted.
    - Snapshots and volumes created from encrypted snapshots are encrypted.
2. **Encrypting Volumes:**
    - **Step 1:** Create a snapshot of the unencrypted volume.
    - **Step 2:** Copy the snapshot and select the encryption option.
    - **Step 3:** Create an AMI from the encrypted snapshot.
    - **Step 4:** Launch a new instance from the AMI.
3. **Remember the Steps:** The process of encrypting an existing unencrypted volume is crucial for the exam. Ensure you understand the sequence: snapshot creation, snapshot copy with encryption, AMI creation, and instance launch from the encrypted AMI.

This summary provides a clear understanding of AWS EBS Encryption, its benefits, and the steps required to encrypt existing unencrypted volumes.

---

## Reference:

[Amazon EBS encryption - Amazon EBS](https://docs.aws.amazon.com/ebs/latest/userguide/ebs-encryption.html)

[How do I unencrypt an encrypted EBS volume?](https://repost.aws/knowledge-center/create-unencrypted-volume-kms-key)