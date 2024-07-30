### Summary: DynamoDB Streams and Global Tables

### DynamoDB Streams

- **Definition**: Time-ordered sequence of item-level changes in a DynamoDB table.
- **Characteristics**:
    - Stored for 24 hours.
    - Changes include inserts, updates, and deletes.
    - Sequence data is broken into shards.
    - Streams can be used with Lambda for functionality similar to stored procedures.

### Global Tables

- **Definition**: Managed multi-master, multi-region replication of DynamoDB tables.
- **Benefits**:
    - Great for globally distributed applications.
    - Multi-region redundancy for disaster recovery or high availability.
    - Built into DynamoDB natively with no need to rewrite applications.
    - Replication latency is typically under one second.

### Setting Up Global Tables

1. **Create a DynamoDB Table**:
    - Specify the table name and partition key.
    - Use default settings (e.g., read/write capacity, auto-scaling).
2. **Enable Global Tables**:
    - Requires DynamoDB Streams to be turned on.
    - Select the region to replicate the table.
    - DynamoDB Streams are automatically enabled for new and old images.
3. **Replication**:
    - Data updates in the primary table are automatically replicated to the selected region.
    - Changes are visible in the replicated table almost immediately.

### Exam Tips

- **DynamoDB Global Tables**:
    - Provides multi-master, multi-region replication.
    - Ideal for applications with global reach needing consistent database access across regions.
    - Based on DynamoDB Streams.
    - Enables multi-region redundancy and high availability without rewriting applications.
    - Replication latency is typically under one second.
- **DynamoDB Streams**:
    - Ensure Streams are enabled to use global tables.
    - Useful for disaster recovery and high availability setups.

By understanding the capabilities and setup of DynamoDB Streams and Global Tables, you will be well-prepared for related questions in the AWS Solutions Architect Associate exam.