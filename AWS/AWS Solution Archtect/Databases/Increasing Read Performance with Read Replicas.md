### Summary of RDS Read Replicas

### Introduction to Read Replicas

- **Read Replica**: A read-only copy of your primary database designed to improve read performance by distributing read requests.
- **Use Case**: Ideal for read-heavy workloads to take the load off the primary database.

### Key Differences: Multi-AZ vs. Read Replicas

- **Multi-AZ**:
    - **Purpose**: Disaster recovery.
    - **Replication**: Automatic failover to a standby database in a different availability zone.
    - **Usage**: High availability and fault tolerance.
    - **Performance**: Does not improve read performance; only used for redundancy.
- **Read Replicas**:
    - **Purpose**: Improve read performance.
    - **Replication**: Creates read-only copies in the same or different availability zones, or even different regions.
    - **Usage**: Offloads read traffic from the primary database.
    - **Performance**: Enhances read performance for read-heavy workloads.

### Key Facts About Read Replicas

- **Automatic Backups**: Must be enabled.
- **Multiple Read Replicas**: Supported for MySQL, MariaDB, PostgreSQL, Oracle, and SQL Server (up to five read replicas per database instance).
- **DNS Endpoint**: Each read replica has its own DNS endpoint.
- **Promotion**: Read replicas can be promoted to become standalone primary databases, breaking the replication link.

### Creating a Read Replica in AWS Console

1. **Navigate to RDS**: Go to RDS in the AWS Management Console.
2. **Select Database**: Choose the primary database for which you want to create a read replica.
3. **Create Read Replica**: Select the option to create a read replica and configure the settings (instance type, region, storage, etc.).
4. **Instance Configuration**: Choose instance class and storage options.
5. **Region Selection**: Optionally, select a different region for cross-region read replicas.
6. **Launch Read Replica**: Complete the setup and launch the read replica.

### Exam Tips

- **Multi-AZ**:
    - Used for disaster recovery.
    - Automatic failover to standby instance.
    - Does not improve read performance.
- **Read Replicas**:
    - Used to scale read performance.
    - Can be in the same AZ, different AZ, or different region.
    - Ideal for read-heavy workloads and business intelligence reporting.
- **Scenario-Based Questions**:
    - If the scenario involves improving read performance, consider read replicas.
    - If the scenario involves high availability and disaster recovery, consider Multi-AZ deployments.

