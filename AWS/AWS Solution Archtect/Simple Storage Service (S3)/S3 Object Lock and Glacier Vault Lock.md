### <span style="color:#00b050">S3 Object Lock and Glacier Vault Lock - Key Points</span>

#### <span style="color:#00b050">S3 Object Lock</span>
- **Purpose**: Prevents objects from being deleted or modified for a fixed amount of time or indefinitely.
- **WORM Model**: Write Once, Read Many; ensures data is immutable.
- **Use Case**: Meets regulatory requirements or adds extra protection against object changes and deletions.

##### <span style="color:#00b050">Modes of S3 Object Lock</span>
1. **Governance Mode**:
   - Most users cannot overwrite or delete object versions or alter lock settings.
   - Some users with special permissions can override settings or delete objects.
   - Use when some level of user override is necessary.

2. **Compliance Mode**:
   - No user, including the root user, can overwrite or delete object versions or alter lock settings.
   - Ensures absolute protection for the duration of the retention period.
   - Use when complete immutability is required.

##### <span style="color:#00b050">Retention Periods and Legal Holds</span>
- **Retention Period**:
  - Protects an object version for a set period (e.g., 10 days, 100 days, 1 year).
  - An S3 timestamp in the object's metadata indicates the expiry.
  - After expiry, objects can be overwritten or deleted unless a legal hold is also applied.

- **Legal Holds**:
  - Prevents object version from being overwritten or deleted.
  - Does not have a specific retention period and remains until removed.
  - Can be applied or removed by users with `S3 put object legal holds` permission.
  
![[Pasted image 20240531132342.png]]
#### <span style="color:#00b050">Glacier Vault Lock</span>
- **Purpose**: Enforces compliance controls for individual S3 Glacier vaults with a vault lock policy.
- **WORM Model**: Write Once, Read Many; ensures data is immutable.
- **Use Case**: Long-term archival with strict compliance needs.

##### <span style="color:#00b050">Features</span>
- **Vault Lock Policy**:
  - Specifies controls such as WORM.
  - Once locked, policies cannot be edited.
  - Ensures immutability and compliance for stored data.

#### <span style="color:#00b050">Exam Tips</span>
1. **S3 Object Lock**:
   - Used for WORM model in S3.
   - Two modes: **Governance Mode** and **Compliance Mode**.
   - Governance Mode: Allows some users to alter or delete objects with special permissions.
   - Compliance Mode: No user can alter or delete objects; absolute protection.
   - Applicable to individual objects or entire buckets.

2. **Glacier Vault Lock**:
   - Used for WORM model in Glacier.
   - Enforces compliance controls for long-term archival.
   - Policies cannot be changed once locked.

3. **Scenarios**:
   - S3 and WORM model: Use S3 Object Lock.
   - Glacier and WORM model: Use Glacier Vault Lock.
   - Governance vs. Compliance Mode: Governance allows some override, compliance allows none.

### Summary
- S3 Object Lock and Glacier Vault Lock provide mechanisms to enforce immutability and compliance through WORM models.
- **S3 Object Lock** offers two modes (Governance and Compliance) and supports retention periods and legal holds.
- **Glacier Vault Lock** provides immutable storage for long-term archival with strict compliance requirements.
- Important for the AWS Certified Solutions Architect - Associate exam to understand use cases and application of these features.