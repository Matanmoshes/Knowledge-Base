### Summary: AWS Databases Review

#### Relational Database Service (RDS)
- **Database Types**: SQL Server, Oracle, MySQL, PostgreSQL, MariaDB, Amazon Aurora.
- **Use Case**: Suitable for online transaction processing (OLTP) workloads like customer orders, banking transactions, and booking systems.
- **Not Suitable for**: Online analytics processing (OLAP); use Redshift instead for data warehousing and large-scale data analysis.

#### Read Replicas vs. Multi-AZ
- **Read Replicas**:
  - **Purpose**: Scaling read performance.
  - **Automatic Backups**: Must be turned on.
  - **Supported Databases**: MySQL, MariaDB, PostgreSQL, Oracle, SQL Server (up to 5 read replicas).
  - **Placement**: Same or cross availability zones, or cross regions.
  - **Use Case**: Great for read-heavy workloads like business intelligence reporting.
![[Pasted image 20240802160845.png]]

- **Multi-AZ**:
  - **Purpose**: Disaster recovery.
  - **Function**: Exact copy of the database in another AZ with automatic failover.
  - **Use Case**: Ensures high availability and data redundancy.

![[Pasted image 20240802160920.png]]

#### Amazon Aurora
- **Compatibility**: MySQL and PostgreSQL.
- **Redundancy**: 2 copies of data in each of 3 AZs (total 6 copies).
- **Snapshots**: Can be shared with other AWS accounts.
- **Replicas**: Aurora, MySQL, PostgreSQL.
- **Failover**: Automated with Aurora replicas.
- **Backups**: Automated by default.
- **Aurora Serverless**: Cost-effective for infrequent or unpredictable workloads.

#### DynamoDB
- **Storage**: SSD.
- **Distribution**: Across 3 geographically distinct data centers.
- **Read Consistency**:
  - **Eventually Consistent Reads**: Default, best performance, data consistency within 1 second.
  - **Strongly Consistent Reads**: Immediate consistency.
- **Transactions**: Supports ACID properties (atomicity, consistency, isolation, durability).
  - **Reads**: Eventual, strong, transactional.
  - **Writes**: Standard, transactional.
  - **Use Case**: Financial transactions, order fulfillment.
- **Backups**:
  - **On-Demand**: Zero impact on performance, retained until deleted, operates in the same region.
  - **Point-in-Time Recovery**: Not enabled by default, allows restoring to any point in the last 35 days.
- **Streams**: Time-ordered sequences of item changes, stored for 24 hours, integrated with Lambda functions.
- **Global Tables**: Multi-master, multi-region replication, needs DynamoDB Streams turned on.

#### Amazon DocumentDB
- **Purpose**: Running MongoDB-compatible workloads on AWS.
- **Use Case**: Migrating MongoDB databases to AWS without refactoring.

#### Amazon Keyspaces
- **Purpose**: Running Apache Cassandra workloads on AWS.
- **Use Case**: Migrating Cassandra clusters to AWS.

#### Amazon Neptune
- **Purpose**: Graph database service.
- **Use Case**: Graph databases for applications involving complex relationships.
- **Exam Tips**: Often used as a distractor; select only for graph database scenarios.

#### Amazon QLDB (Quantum Ledger Database)
- **Purpose**: Immutable ledger database for cryptographically verifiable transaction logs.
- **Use Case**: Financial transactions, supply chain tracking, claims history.
- **Exam Tips**: Select only for scenarios requiring immutable databases.

#### Amazon Timestream
- **Purpose**: Time-series data analysis.
- **Use Case**: IoT sensor data, real-time analytics, DevOps monitoring.
- **Exam Tips**: Select for scenarios involving time-series data collection and analysis.

By understanding these key concepts and their use cases, you'll be better prepared for AWS Solutions Architect Associate exam questions related to database services.