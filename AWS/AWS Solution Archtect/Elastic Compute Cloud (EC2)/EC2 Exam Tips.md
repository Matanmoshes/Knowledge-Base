### <span style="color:#ffc000">Comprehensive Review of EC2 - Key Points</span>

#### <span style="color:#ffc000">Introduction to EC2</span>
- **Definition**: EC2 (Elastic Compute Cloud) is like a virtual machine hosted in AWS.
- **Flexibility**: Select the capacity you need, grow and shrink as needed, pay only for what you use.
- **Provisioning Time**: Takes minutes to provision, unlike traditional on-premises setups.

#### <span style="color:#ffc000">EC2 Instance Pricing Options</span>
1. **On-Demand**:
   - **Payment**: Pay by the second or hour.
   - **Use Case**: Great for flexibility, no long-term contracts.

2. **Spot**:
   - **Payment**: Purchase unused capacity at up to 90% discount.
   - **Use Case**: Suitable for applications with flexible start/end times.

3. **Reserved**:
   - **Payment**: Reserve capacity for 1 to 3 years, with up to 72% discount.
   - **Use Case**: Ideal for known, fixed requirements.

4. **Dedicated**:
   - **Payment**: Physical server dedicated for your use.
   - **Use Case**: Compliance requirements, server-bound licenses.

#### <span style="color:#ffc000">AWS Command Line Interface (CLI)</span>
- **Least Privilege**: Always give users the minimum required access.
- **Groups**: Create IAM groups and assign users to groups.
- **Key Tips**:
  - **Secret Access Key**: Only visible once; regenerate if lost.
  - **No Sharing**: Each developer should have their own key pair.
  - **Supported Platforms**: CLI supported on Linux, Windows, and macOS.

#### <span style="color:#ffc000">Roles</span>
- **Preferred Option**: Use roles for security to avoid hard coding credentials.
- **Policy Management**: Roles' permissions are controlled by policies.
- **Updates**: Policy updates take effect immediately.
- **Attachment/Detachment**: Attach or detach roles without stopping instances.

#### <span style="color:#ffc000">Security Groups</span>
- **Changes**: Take effect immediately.
- **Flexibility**: Any number of instances within a group; multiple groups per instance.
- **Defaults**: All inbound traffic blocked by default; all outbound traffic allowed.

#### <span style="color:#ffc000">Bootstrap Scripts</span>
- **Definition**: Scripts run at instance startup with root-level permissions.
- **Use Case**: Install applications, perform updates, etc.
- **User Data vs. Metadata**:
  - **User Data**: Bootstrap scripts.
  - **Metadata**: Data about EC2 instances.

#### <span style="color:#ffc000">EC2 Networking</span>
1. **ENI (Elastic Network Interface)**:
   - **Use Case**: Basic networking, separate management, logging, or monitoring networks.

2. **Enhanced Networking (EN)**:
   - **Use Case**: Speeds between 10-100 Gbps, high throughput.

3. **EFA (Elastic Fabric Adapter)**:
   - **Use Case**: High-performance computing, machine learning, OS-level bypass.

#### <span style="color:#ffc000">Placement Groups</span>
1. **Clustered**:
   - **Use Case**: Low network latency, high network throughput.

2. **Spread**:
   - **Use Case**: Critical instances, need separation.

3. **Partition**:
   - **Use Case**: Multiple instances (e.g., HDFS, HBase, Cassandra).

- **Exam Tips**:
  - **Clustered**: Cannot span multiple availability zones.
  - **Spread/Partition**: Can span multiple availability zones.
  - **Instance Types**: Only certain types (compute, GPU, memory, storage optimized) in placement groups.
  - **Homogeneous Instances**: Recommended in clustered groups.
  - **Movement**: Instances must be stopped to move or remove them from a group.

#### <span style="color:#ffc000">Dedicated Hosts</span>
- **Use Case**: Special licensing or compliance requirements.
- **Definition**: Physical server dedicated for your use, supporting various licensing models.

#### <span style="color:#ffc000">Spot Instances and Spot Fleets</span>
- **Spot Instances**: Save up to 90% of on-demand costs; suitable for non-persistent storage.
- **Spot Fleets**: Collection of spot instances (and optionally on-demand instances) to meet target capacity.

#### <span style="color:#ffc000">VMware on AWS</span>
- **Definition**: Extend private VMware cloud to AWS public cloud.
- **Use Cases**: Cloud migration, disaster recovery.

#### <span style="color:#ffc000">AWS Outposts</span>
- **Definition**: Extending AWS to your data center.
- **Family Members**:
  - **Outposts Racks**: Large deployments in data centers.
  - **Outposts Servers**: Smaller deployments (e.g., retail shops).

This comprehensive review encapsulates the key concepts and best practices for EC2, essential for both practical AWS usage and the AWS Certified Solutions Architect - Associate exam.