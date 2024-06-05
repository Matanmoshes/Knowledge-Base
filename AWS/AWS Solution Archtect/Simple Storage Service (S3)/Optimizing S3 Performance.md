### <span style="color:#00b050">Optimizing S3 Performance - Key Points</span>

#### <span style="color:#00b050">S3 Prefixes</span>
- **Definition**: Folders or directories inside your S3 buckets.
- **Example**:
  - Bucket name: `mybucket`
  - Prefix: `folder1/subfolder1`
  - Object name: `myfile.jpeg`
- **Performance Optimization**:
  - S3 provides high performance with low latency.
  - 5,500 get or head requests per second per prefix.
  - 3,500 put, copy, post, or delete requests per second per prefix.
  - More prefixes = better performance.
  - Example: 2 prefixes can handle 11,000 requests per second; 4 prefixes can handle 22,000 requests per second.

#### <span style="color:#00b050"> KMS Limitations</span>
- **KMS (Key Management Service)**: Used for encrypting and decrypting objects.
- **Limits**:
  - Region-specific request limits (5,500, 10,000, or 30,000 requests per second).
  - Uploading and downloading data count towards KMS quota.
  - No option to request a quota increase for KMS.
- **Recommendation**: Consider using native S3 encryption if hitting KMS limits.

#### <span style="color:#00b050">S3 Performance for Uploads</span>
- **Multipart Uploads**:
  - Recommended for files > 100 MB.
  - Required for files > 5 GB.
  - **Process**: Splits large files into smaller parts, uploads parts in parallel.
  - **Benefit**: Increases efficiency and performance.

#### <span style="color:#00b050">S3 Performance for Downloads</span>
- **S3 Byte-Range Fetches**:
  - **Process**: Downloads specific byte ranges of a file in parallel.
  - **Benefit**: Increases download performance and resilience (failure affects only specific byte range).

#### <span style="color:#00b050">Exam Tips</span>
1. **Prefixes**:
   - Understand what a prefix is (folders and subfolders in an S3 bucket).
   - More prefixes lead to higher performance (more requests per second).
2. **KMS Limitations**:
   - Know the request limits for KMS.
   - Uploading/downloading counts towards KMS quota.
   - Consider native S3 encryption for high request scenarios.
3. **Multipart Uploads**:
   - Use for files > 100 MB.
   - Required for files > 5 GB.
   - Enhances upload performance by parallelizing uploads.
4. **S3 Byte-Range Fetches**:
   - Enhances download performance by parallelizing downloads.
   - Specify byte ranges to download parts of a file concurrently.

### <span style="color:#00b050">Summary</span>
- **S3 Prefixes**: Use multiple prefixes to enhance performance by spreading read/write requests.
- **KMS Limitations**: Be aware of the request limits for encryption/decryption operations.
- **Multipart Uploads**: Improve upload performance for large files by splitting them into smaller parts.
- **S3 Byte-Range Fetches**: Improve download performance by downloading specific byte ranges in parallel.

Understanding these key points will help you optimize S3 performance and prepare for the AWS Certified Solutions Architect - Associate exam.