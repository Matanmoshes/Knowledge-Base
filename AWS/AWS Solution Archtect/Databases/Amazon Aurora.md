### Summary of Amazon Aurora

### Introduction to Amazon Aurora

- **Aurora**: Amazon's proprietary database, compatible with MySQL and PostgreSQL.
- **Performance**: Offers 5x better performance than MySQL and 3x better than PostgreSQL.
- **Cost-Effective**: Provides high-end performance at a lower price point.

### Basics of Aurora

- **Scalability**:
    - Starts at 10GB, scales in 10GB increments up to 128TB.
    - **Storage Auto Scaling**: Automatically scales as data is added.
    - **Compute Resources**: Up to 96 vCPUs and 768GB of memory.
- **Redundancy**:
    - Two copies of data in each availability zone.
    - Minimum of three availability zones, totaling six copies of data.
    - Self-healing storage with continuous scanning and automatic repair of data blocks.

### Scaling and Availability

- **Fault Tolerance**:
    - Can handle the loss of up to two copies without affecting write availability.
    - Can handle the loss of three copies without affecting read availability.

### Aurora Replicas

- **Types of Replicas**:
    - **Aurora Replicas**: Up to 15 read replicas.
    - **MySQL/PostgreSQL Replicas**: Up to 5 read replicas each.
- **Replication Characteristics**:
    - **Aurora Replicas**: Asynchronous replication with millisecond latency, low performance impact, automated failover, in-region.
    - **MySQL Replicas**: Asynchronous replication with second latency, high performance impact, no automated failover, cross-region supported.

### Backups with Aurora

- **Automated Backups**: Always enabled, no performance impact.
- **Manual Snapshots**: Can be taken anytime, no performance impact, shareable with other AWS accounts.

### Aurora Serverless

- **Description**: On-demand auto-scaling configuration for MySQL-compatible and PostgreSQL-compatible editions of Aurora.
- **Functionality**: Automatically starts up, shuts down, and scales capacity based on application needs.
- **Cost-Effectiveness**: Only pay for what you use, suitable for unpredictable workloads.

### Use Cases for Aurora Serverless

- **Ideal for**: Infrequent, intermittent, or unpredictable workloads.

### Exam Tips

- **Redundancy**: Aurora maintains at least six copies of your data across three availability zones.
- **Sharing Snapshots**: You can share Aurora snapshots with other AWS accounts.
- **Types of Replicas**: Aurora replicas, MySQL replicas, PostgreSQL replicas.
    - Automated failover only available with Aurora replicas.
- **Backups**: Automated backups are on by default; you can take manual snapshots anytime.
- **Aurora Serverless**: Use for cost-effective solutions for infrequent, intermittent, or unpredictable workloads.

