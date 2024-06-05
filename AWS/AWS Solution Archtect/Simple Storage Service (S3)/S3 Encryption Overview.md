### <span style="color:#00b050">S3 Encryption Overview - Key Points</span>

#### Types of Encryption
1. **Encryption in Transit**:
   - **Purpose**: Secures data while it is being sent to and from your S3 bucket.
   - **Method**: Uses SSL/TLS (HTTPS) to encrypt data, ensuring it cannot be intercepted or tampered with during transfer.

2. **Encryption at Rest**:
   - **Purpose**: Protects data stored in S3.
   - **Types**:
     1. **SSE-S3 (Server-Side Encryption with S3 Managed Keys)**:
        - AWS manages the encryption keys.
        - Uses AES-256 encryption.
     2. **SSE-KMS (Server-Side Encryption with Key Management Service)**:
        - Allows you to use AWS KMS to manage encryption keys.
     3. **SSE-C (Server-Side Encryption with Customer-Provided Keys)**:
        - You provide the encryption keys.
     4. **Client-Side Encryption**:
        - Encrypt data on your side before uploading to S3.
        - You manage the encryption and decryption process.

![[Pasted image 20240531171853.png]]

#### <span style="color:#00b050">Enforcing Server-Side Encryption with Bucket Policies</span>
- **Purpose**: Ensure that all objects uploaded to your S3 bucket are encrypted.
- **Method**:
  - Use bucket policies to require encryption.
  - Include `x-amz-server-side-encryption` parameter in the request header.
  - **Options**:
    - `x-amz-server-side-encryption: AES256` (S3 managed keys)
    - `x-amz-server-side-encryption: aws:kms` (KMS managed keys)

#### <span style="color:#00b050">Implementing Encryption in AWS Console</span>
1. **Creating a Bucket with Encryption**:
   - Go to S3 and create a new bucket.
   - By default, server-side encryption is enabled.
   - Choose between S3 managed keys (SSE-S3), KMS (SSE-KMS), or dual-layer encryption.

2. **Verifying Encryption**:
   - Go to the bucket and click on an object.
   - Scroll down to **Server-side encryption settings** to see the encryption details.

#### <span style="color:#00b050"> Exam Tips</span>
1. **Know the Types of Encryption**:
   - Understand encryption in transit (SSL/TLS) and encryption at rest (SSE-S3, SSE-KMS, SSE-C, client-side encryption).
2. **Enforce Encryption with Bucket Policies**:
   - Use bucket policies to deny PUT requests that do not include the required encryption parameters.
   - Understand the parameters (`x-amz-server-side-encryption: AES256` and `x-amz-server-side-encryption: aws:kms`).
3. **Default Encryption**:
   - Remember that server-side encryption is enabled by default for all new S3 buckets.
4. **Client-Side Encryption**:
   - Know that client-side encryption is managed by the user before uploading to S3.

### <span style="color:#00b050">Summary</span>
- **Encryption in Transit**: Protects data being sent to/from S3 using SSL/TLS.
- **Encryption at Rest**: Protects data stored in S3 with several options (SSE-S3, SSE-KMS, SSE-C, client-side encryption).
- **Enforcing Encryption**: Use bucket policies to ensure data is encrypted at upload.
- **AWS Console**: Encryption is enabled by default when creating new buckets.

Understanding these key points will help you effectively secure your S3 buckets and prepare for the AWS Certified Solutions Architect - Associate exam.