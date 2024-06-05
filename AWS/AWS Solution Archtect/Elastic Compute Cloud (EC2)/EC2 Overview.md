### <span style="color:#ffc000">AWS EC2 Overview - Key Points</span>

#### <span style="color:#ffc000">What is EC2?</span>
- **EC2** stands for **Elastic Compute Cloud**.
- It provides **secure, resizable compute capacity** in the cloud.
- EC2 allows you to run virtual servers (instances) in AWS instead of your own data center.
- It is designed to make web-scale cloud computing easier for developers.

#### <span style="color:#ffc000">EC2 Basics</span>
- **Launch Year**: 2006, transforming the cloud computing industry.
- **Instance Control**: Users have complete control over their instances.
- **Capacity Management**: You can grow and shrink your compute capacity as needed.
- **Payment**: Pay only for the capacity you use.

#### <span style="color:#ffc000">Key Concepts</span>
- **Instance Types**: Various configurations of CPU, memory, storage, and networking capacity.
- **Elasticity**: Scale resources up or down based on demand.
- **Security Groups**: Virtual firewalls controlling the traffic to and from your instances.

#### <span style="color:#ffc000">Pricing Options</span>
##### 1. **<span style="color:#d68a8a">On-Demand Instances</span>**
- **Definition**: Pay for compute capacity by the hour or second (with a minimum of 60 seconds) with no long-term commitments or upfront payments.
- **Use Case**: Ideal for short-term, spiky, or unpredictable workloads that cannot be interrupted. It's also suitable for first-time applications or testing.
- **Advantages**: No upfront costs, flexible, and no long-term commitment.
- **Billing**: Per second or per hour.

##### 2. **<span style="color:#d68a8a">Reserved Instances (RIs)</span>**
- **Definition**: Commit to use for a 1-year or 3-year term, which can offer significant discounts (up to 75%) compared to On-Demand pricing.
- **Types**:
  - **Standard RIs**: Offer the most significant discount but require a long-term commitment.
  - **Convertible RIs**: Offer a lower discount compared to Standard RIs but provide the flexibility to change instance attributes if your needs change.
- **Use Case**: Suitable for steady-state or predictable usage applications.
- **Advantages**: Significant cost savings over time.
- **Billing**: Can be paid upfront, partially upfront, or no upfront (monthly).

##### 3. **<span style="color:#d68a8a">Spot Instances</span>**
- **Definition**: Purchase unused EC2 capacity at a significant discount (up to 90%) compared to On-Demand prices.
- **Use Case**: Ideal for flexible applications that can be interrupted, such as batch processing, big data analysis, or fault-tolerant workloads.
- **Advantages**: Extremely cost-effective.
- **Billing**: Bidding system – you specify the maximum price you're willing to pay per hour.

##### 4. **<span style="color:#d68a8a">Dedicated Hosts</span>**
- **Definition**: Physical EC2 servers dedicated for your use, which can help you meet compliance requirements and reduce costs by allowing you to use existing server-bound software licenses.
- **Use Case**: Suitable for regulatory requirements, licensing that doesn’t support multi-tenancy or cloud deployments.
- **Advantages**: Dedicated hardware, control over instance placement.
- **Billing**: On-demand or with a reservation (1-year or 3-year term).


#### <span style="color:#ffc000">Availability and Durability</span>
- **Availability**: EC2 is designed for high availability across multiple Availability Zones.
- **Durability**: Data stored on instances can be backed up using EBS snapshots or stored in S3 for redundancy.


#### <span style="color:#ffc000">Exam Tips</span>
- **Pricing Models**: Understand On-Demand, Reserved, Spot, and Dedicated Hosts.
- **Use Cases**: Know the scenarios for each pricing model.
- **Elasticity and Flexibility**: EC2 instances provide flexible, scalable compute capacity.
- **Security Groups and IAM Roles**: Essential for managing access and permissions.

