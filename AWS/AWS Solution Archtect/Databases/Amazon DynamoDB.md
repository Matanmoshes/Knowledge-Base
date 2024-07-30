### Summary of Amazon DynamoDB

### Introduction to DynamoDB

- **DynamoDB**: Amazon's proprietary NoSQL database service.
- **Key Features**:
    - Fast and flexible for applications requiring consistent, single-digit millisecond latency.
    - Fully managed and supports both document and key-value data models.
    - Suitable for mobile, web, gaming, ad-tech, IoT, and many other applications.

### Key Facts about DynamoDB

- **Storage**: Uses SSD storage.
- **Geographic Distribution**: Data is spread across three geographically distinct data centers.
- **Read Consistency Models**:
    - **Eventually Consistent Reads**: Consistency across all copies of data is usually reached within one second. Best for read performance.
    - **Strongly Consistent Reads**: Returns a result that reflects all writes received a successful response before the read.

### DynamoDB Accelerator (DAX)

- **Description**: Fully managed, highly available in-memory caching service for DynamoDB.
- **Performance**: Provides up to 10x performance improvement, reducing request times from milliseconds to microseconds.
- **Operation**:
    - Applications interact directly with DAX.
    - If data is not in the cache, DAX fetches it from DynamoDB and caches it.
    - Simplifies application design by removing the need to manage caching logic.

### On-Demand Capacity

- **Pricing**: Pay-per-request, balancing cost and performance.
- **Benefits**:
    - No minimum capacity required.
    - Ideal for unpredictable workloads like new product launches.
    - More expensive per request compared to provisioned capacity.

### Security

- **Encryption**: Data encrypted at rest using AWS KMS.
- **Connectivity**:
    - Supports site-to-site VPN and Direct Connect for secure connections.
- **Access Control**: Integrated with IAM for fine-grained access control.
- **Monitoring**: Integrates with CloudWatch and CloudTrail for performance monitoring and auditing changes.
- **VPC Endpoints**: Allows direct communication with DynamoDB without leaving the AWS network.

### Exam Tips

- **Storage**: DynamoDB uses SSD storage.
- **Data Distribution**: Data is spread across three distinct data centers.
- **Read Consistency**:
    - **Eventually Consistent Reads**: Provides best read performance, consistency within one second.
    - **Strongly Consistent Reads**: Reflects all successful writes before the read.
- **DAX**: Improves performance by caching data, simplifies application design.
- **On-Demand Capacity**: No minimum capacity, suitable for unpredictable workloads, higher cost per request.
- **Security**: Data encrypted at rest, supports secure connections, integrates with IAM, CloudWatch, CloudTrail, and VPC endpoints.



---

[Amazon DynamoDB FAQs | NoSQL Key-Value Database | Amazon Web Services](https://aws.amazon.com/dynamodb/faqs/)

[Amazon DynamoDB announces the new Amazon DynamoDB Standard-Infrequent Access table class, which helps you reduce your DynamoDB costs by up to 60 percent](https://aws.amazon.com/about-aws/whats-new/2021/12/amazon-dynamodb-standard-infrequent-access-table-class/)