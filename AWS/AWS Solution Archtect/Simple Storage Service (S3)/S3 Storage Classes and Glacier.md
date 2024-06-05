### <span style="color:#00b050">S3 Storage Classes and Glacier - Key Points</span>

#### Overview
- Different S3 storage classes and Glacier options cater to various use cases based on access frequency, performance, and cost.
- Important to know these classes for the exam as scenario-based questions will test your knowledge on selecting the best storage class for different needs.

#### <span style="color:#00b050">S3 Storage Classes</span>
1. **<span style="color:#ffc000">S3 Standard</span>**:
   - High availability and durability.
   - Data stored across multiple devices in multiple facilities (≥ 3 Availability Zones).
   - 99.99% availability and 11 nines (99.999999999%) durability.
   - Designed for frequent access.
   - Use cases: Websites, content distribution, mobile and gaming apps, big data analytics.
   
2. **<span style="color:#ffc000">S3 Standard-Infrequently Accessed (S3 Standard-IA)</span>**:
   - For infrequently accessed data requiring rapid access.
   - Lower storage cost but retrieval fees apply.
   - 99.9% availability and 11 nines durability.
   - Use cases: Long-term storage, backups, disaster recovery.
   
3. **<span style="color:#ffc000">S3 One Zone-Infrequently Accessed (S3 One Zone-IA)</span>**:
   - Data stored in a single Availability Zone (20% less cost than Standard-IA).
   - 99.5% availability and 11 nines durability.
   - Use cases: Non-critical, long-lived, infrequently accessed data.
   
4. **<span style="color:#ffc000">S3 Intelligent Tiering</span>**:
   - Automatically moves data to the most cost-effective tier based on access patterns.
   - 99.9% availability and 11 nines durability.
   - Optimizes costs for unpredictable access patterns.
   - Monthly fee per 1,000 objects.

#### <span style="color:#00b050">Glacier Storage Classes</span>
1. **Glacier Instant Retrieval**:
   - Long-term archiving with instant retrieval.
   - Use cases: Critical data needing infrequent but instant access.
   
2. **Glacier Flexible Retrieval**:
   - For archives not needing immediate access but requiring flexibility to retrieve large datasets.
   - Retrieval time: Minutes to 12 hours.
   - Use cases: Backup, disaster recovery.
   
3. **Glacier Deep Archive**:
   - Cheapest storage class.
   - Designed for data retained for 7-10 years or longer.
   - Retrieval time: 12 to 48 hours.
   - Use cases: Long-term regulatory data storage.

#### <span style="color:#00b050">Performance and Cost Considerations</span>
- **Performance**:
  - S3 Standard and Intelligent Tiering have the highest availability and instant access.
  - Infrequent Access classes and Glacier offer trade-offs between cost and retrieval speed.
- **Cost**:
  - S3 Standard is the most expensive.
  - Intelligent Tiering optimizes costs for variable access patterns.
  - Glacier is the cheapest, with Deep Archive being the least expensive option but with the longest retrieval times.
  
![[Pasted image 20240531123922.png]]
#### <span style="color:#00b050">Exam Tips</span>
- **Know the Use Cases**:
  - **S3 Standard**: Frequently accessed data, websites, content distribution.
  - **S3 Standard-IA**: Critical but infrequently accessed data (e.g., backups, disaster recovery).
  - **S3 One Zone-IA**: Non-critical, long-lived, infrequently accessed data.
  - **S3 Intelligent Tiering**: Data with unpredictable access patterns.
  - **Glacier Instant Retrieval**: Critical data needing instant access.
  - **Glacier Flexible Retrieval**: Data not requiring immediate access but needing flexible, large-scale retrieval.
  - **Glacier Deep Archive**: Cheapest storage, suitable for long-term, rarely accessed data with long retrieval times.

![[Pasted image 20240531124318.png]]


