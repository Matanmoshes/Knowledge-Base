### <span style="color:#ffc000">Optimizing EC2 Using Placement Groups - Key Points</span>

#### <span style="color:#ffc000">Introduction</span>
- **Objective**: Learn about optimizing EC2 instances using placement groups.
- **Components**:
  - Three types of placement groups.
  - Detailed look at clustered, spread, and partition placement groups.
  - Exam tips.
  - [AWS Documentation](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/placement-groups.html)
  
#### <span style="color:#ffc000">Types of Placement Groups</span>
1. **<span style="color:#d68a8a">Clustered Placement Groups</span>**:
   - **Definition**: Grouping of instances within a single availability zone.
   - **Use Case**: Applications needing low network latency, high network throughput, or both.
   - **Instance Types**: Only certain EC2 instances can be launched into a clustered placement group.
 
![[Pasted image 20240608141641.png]]

2. **<span style="color:#d68a8a">Spread Placement Groups</span>**:
   - **Definition**: Group of instances placed on distinct underlying hardware.
   - **Use Case**: Critical applications needing instances on separate hardware to avoid single points of failure.
   - **Application**: Primary and secondary databases on different hardware to ensure separation.

3. **<span style="color:#d68a8a">Partition Placement Groups</span>**:
   - **Definition**: Each partition has its own set of racks with separate network and power sources.
   - **Use Case**: Isolating the impact of hardware failure within applications.
   - **Application**: Large distributed systems like HDFS, HBase, Cassandra needing dedicated network and power sources.

#### <span style="color:#ffc000">Placement Groups in Detail</span>

1. **<span style="color:#d68a8a">Clustered Placement Groups</span>**:
   - **Description**: Instances are close together within the same availability zone, enhancing communication speed.
   - **Best For**: High-performance compute applications.
   - **Limitations**: Cannot span multiple availability zones.

2. **<span style="color:#d68a8a">Spread Placement Groups</span>**:
   - **Description**: Instances are placed on distinct hardware to minimize the impact of hardware failure.
   - **Best For**: Applications with a small number of critical instances.
   - **Benefits**: Reduces the risk of simultaneous failures.

3. **<span style="color:#d68a8a">Partition Placement Groups</span>**:
   - **Description**: Instances are divided into partitions with separate network and power sources.
   - **Best For**: Applications needing isolation for failure mitigation.
   - **Capabilities**: Can span multiple availability zones, each partition having its dedicated infrastructure.

#### <span style="color:#ffc000">Exam Tips</span>
1. **Placement Group Types**:
   - **Clustered Placement Group**: Use for high performance and low latency requirements.
   - **Spread Placement Group**: Use for critical instances needing hardware separation.
   - **Partition Placement Group**: Use for large distributed systems needing dedicated infrastructure.

2. **Limitations and Recommendations**:
   - **Clustered Placement Groups**: Cannot span multiple availability zones.
   - **Spread and Partition Placement Groups**: Can span multiple availability zones.
   - **Instance Types**: Only compute-optimized, GPU-optimized, memory-optimized, or storage-optimized instances can be used.
   - **Homogeneous Instances**: Recommended for clustered placement groups.
   - **Merging Groups**: Not possible to merge two different placement groups.
   - **Moving Instances**: Must be in a stopped state to move into a placement group, done via command line or SDK, not via console.

#### <span style="color:#ffc000">Practical Steps</span>
- **Clustered Placement Groups**: Use for applications like HPC that need very low latency.
- **Spread Placement Groups**: Use for applications like critical databases where hardware isolation is crucial.
- **Partition Placement Groups**: Use for large-scale applications needing partitioning for reliability and failure isolation.

This summary provides a comprehensive guide to understanding and optimizing EC2 instances using placement groups, essential for managing network performance and reliability in AWS, and preparing for the AWS Certified Solutions Architect - Associate exam.