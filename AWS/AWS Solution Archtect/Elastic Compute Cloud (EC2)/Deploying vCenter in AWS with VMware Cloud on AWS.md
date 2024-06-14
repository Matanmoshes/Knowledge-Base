### <span style="color:#ffc000">Deploying vCenter in AWS with VMware Cloud on AWS - Key Points</span>

[AWS Documentation](https://aws.amazon.com/vmware/vmwarecloudonaws/)
#### <span style="color:#ffc000">Introduction</span>
- **Objective**: Understand why and how to deploy vCenter in AWS with VMware Cloud on AWS.
- **Components**:
  - Reasons for using VMware on AWS
  - Use cases for VMware
  - Deployment process
  - Exam tips

#### <span style="color:#ffc000">Why Use VMware on AWS?</span>
- **VMware's Legacy**: VMware predates AWS and is widely used for private cloud deployments.
- **Hybrid Cloud Strategy**: Organizations leverage AWS services while maintaining their VMware environments.
  
#### <span style="color:#ffc000">Use Cases for VMware on AWS</span>
1. **Hybrid Cloud**:
   - **Definition**: Connects on-premises cloud to AWS public cloud.
   - **Use Case**: Managing hybrid workloads with varying resource needs.

2. **Cloud Migration**:
   - **Definition**: Moving from legacy private cloud to AWS.
   - **Use Case**: Migrating existing environments to AWS using VMware's built-in tools.

3. **Disaster Recovery**:
   - **Definition**: Inexpensive disaster recovery environment on AWS.
   - **Use Case**: Utilizing VMware's disaster recovery technology.

4. **Leveraging AWS Services**:
   - **Definition**: Using AWS services like Lambda, API Gateway, etc.
   - **Use Case**: Updating applications or creating new ones using AWS services.

#### <span style="color:#ffc000">VMware Cloud on AWS Deployment</span>
- **Infrastructure**:
  - Runs on dedicated hardware hosted in AWS.
  - Each host has:
    - 2 sockets with 18 cores per socket.
    - 512 GB of RAM.
    - 15.2 TB of raw SSD storage.
  - Each host can run multiple VMware instances (up to hundreds).
  - Clusters start with 2 hosts and can scale up to 16 hosts.

- **Deployment Process**:
  - **Order**: VMware Cloud on AWS is ordered through VMware.
  - **Clusters**: vCenter clusters are deployed on AWS for running VMware instances.

#### <span style="color:#ffc000">Exam Tips</span>
- **Remember**:
  - VMware on AWS enables hybrid cloud, cloud migration, disaster recovery, and leveraging AWS services.
  - Deployment involves dedicated hardware and vCenter clusters.
  - VMware Cloud on AWS is ordered through VMware.
- **Key Point for Exam**: You can run vCenter on AWS, but it needs to be ordered through VMware.

This summary provides a concise guide to deploying vCenter in AWS with VMware Cloud on AWS, essential for understanding hybrid cloud strategies and preparing for the AWS Certified Solutions Architect - Associate exam.