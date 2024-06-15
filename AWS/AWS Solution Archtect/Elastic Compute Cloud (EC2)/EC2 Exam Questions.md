### Question

What is the EC2 metadata URL?

1. `http://127.0.0.2/latest/meta-data/`
2. `http://169.254.169.254/latest/meta-data/`
3. `http://255.169.255.169/latest/meta-data/`
4. `http://255.255.255.254/latest/meta-data/`

### Answer

The correct EC2 metadata URL is:

`http://169.254.169.254/latest/meta-data/`

### Explanation

Amazon EC2 instances use a special IP address to access instance metadata and user data. The correct IP address is `169.254.169.254`. This is a link-local address, which is a special range of IP addresses used for communication within the local network segment. By querying `http://169.254.169.254/latest/meta-data/`, you can retrieve information such as the instance ID, instance type, and security groups associated with your EC2 instance. This metadata service is critical for managing and configuring EC2 instances dynamically from within the instance itself. The other IP addresses listed are incorrect and will not provide access to the EC2 metadata.

```shell
TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \
&& curl -H "X-aws-ec2-metadata-token: $TOKEN" http://169.254.169.254/latest/meta-data/
```

 [Retrieve Instance Metadata](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instancedata-data-retrieval.html "null")

---

### Question

When would you want to use a cluster placement group?

1. When you want to reduce network latency in your application
2. When your application acts as a high-performance messaging queue
3. When you need a HA database solution
4. When you want to ensure a 100% uptime for your application

### Answer

You would want to use a cluster placement group:

**When you want to reduce network latency in your application.**

### Explanation

A cluster placement group is a logical grouping of instances within a single Availability Zone in AWS. Using a cluster placement group is beneficial when you need to achieve the lowest possible network latency or the highest network throughput for your applications. Instances in a cluster placement group are physically located close to each other in the data center, which provides low-latency connectivity.

This configuration is particularly useful for applications that require high-performance computing (HPC), big data processing, or other scenarios where rapid communication between instances is essential. Examples include high-performance messaging queues and computational clusters, but the primary driver is to reduce network latency and improve throughput.

The other options listed are not the primary reasons for using a cluster placement group:
- High Availability (HA) database solutions typically benefit more from other strategies like multi-AZ deployments.
- Ensuring 100% uptime requires multiple strategies, including redundancy across multiple availability zones or regions, rather than just placement groups.

[Cluster Placement Groups](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/placement-groups.html#placement-groups-cluster "null")

---

### Question

What is the minimum time that AWS requires you to keep an EC2 instance online after you've turned it on?

1. None (no minimum run time)
2. 1 hour
3. 15 minutes
4. 1 day

### Answer

**None (no minimum run time)**

### Explanation

AWS does not impose a minimum run time requirement for EC2 instances. You are billed on a per-second basis for the instances you use, with a minimum charge of 60 seconds. This means you can start and stop your instances as needed, and you will only be charged for the time the instances are running. This flexibility allows you to manage costs effectively and use instances only when needed without being locked into a specific minimum run time.

[How Are Amazon EC2 Instance Hours Billed?](https://aws.amazon.com/premiumsupport/knowledge-center/ec2-instance-hour-billing/ "null")

---

### Question

When would you need to create an EC2 Dedicated Instance?

1. When you need the cheapest price for an instance
2. When you have an auditing requirement to run your hosts on single-tenant hardware
3. When you need to make sure that AWS support can assist you with a hardware failure
4. When you want to ensure that your instance will never fail

### Answer

You would need to create an EC2 Dedicated Instance:

**When you have an auditing requirement to run your hosts on single-tenant hardware.**

### Explanation

An EC2 Dedicated Instance is a type of Amazon EC2 instance that runs on single-tenant hardware, which means that the physical server is dedicated to a single customer. This is particularly important for organizations with stringent compliance and regulatory requirements that mandate physical isolation from other customers' instances. Dedicated Instances are also useful when you need to control instance placement for licensing purposes or to meet specific performance requirements.

The other options listed are not the primary reasons for choosing Dedicated Instances:
- **Cheapest price**: Dedicated Instances are generally more expensive than shared instances due to the dedicated hardware.
- **AWS support for hardware failure**: AWS support assists with hardware failures regardless of instance type.
- **Ensuring that your instance will never fail**: No instance type can guarantee that an instance will never fail. High availability strategies typically involve redundancy across multiple instances and Availability Zones.

[Amazon EC2 Dedicated Instances](https://aws.amazon.com/ec2/pricing/dedicated-instances/ "null")

---


### Question

Why would you want to spin up an EC2 Dedicated Host?

1. Because you have sensitive workloads that you don't want AWS to see
2. Because you need to create a cluster of EC2 instances
3. Because your application has hardware-specific licensing requirements
4. Because your boss told you to

### Answer

You would want to spin up an EC2 Dedicated Host:

**Because your application has hardware-specific licensing requirements.**

### Explanation

An EC2 Dedicated Host provides a physical server fully dedicated to your use. This means you have visibility and control over the placement of your instances on the server, which can be crucial for meeting compliance, regulatory, or licensing requirements. Dedicated Hosts are often used when you have applications with specific licensing terms that require dedicated physical hardware, such as certain enterprise software that is licensed per physical core or socket.

The other options listed are not the primary reasons for choosing Dedicated Hosts:
- **Sensitive workloads**: While Dedicated Hosts offer isolation, AWS already provides robust security measures for all instances, regardless of type.
- **Creating a cluster of EC2 instances**: This can be achieved with standard EC2 instances or cluster placement groups, and does not necessarily require a Dedicated Host.
- **Because your boss told you to**: This is not a technical reason; decisions should be based on specific technical requirements and business needs.

[Amazon EC2 Dedicated Hosts](https://aws.amazon.com/ec2/dedicated-hosts/ "null")

---

### Question

Which of the following is not a valid use case for IAM roles?

1. Allowing a user from another AWS account to assume a role in your account and interact with your AWS architecture.
2. Defining a set of permissions for an IAM role, and directly associating that role with an IAM group.
3. Attaching a role to an EC2 instance to allow it to talk to other AWS resources.
4. Providing access to your AWS resources to users that are authenticated using an external identity provider.

### Answer

Defining a set of permissions for an IAM role, and directly associating that role with an IAM group.

### Explanation

IAM roles are designed to be assumed by entities such as users, applications, or services, allowing them to take on specific permissions temporarily. Here’s a breakdown of the use cases:

1. **Allowing a user from another AWS account to assume a role in your account and interact with your AWS architecture**: This is a common use case for cross-account access.
2. **Defining a set of permissions for an IAM role, and directly associating that role with an IAM group**: This is not a valid use case. IAM roles cannot be directly associated with IAM groups. Roles are meant to be assumed by users or services, not groups.
3. **Attaching a role to an EC2 instance to allow it to talk to other AWS resources**: This is a valid use case known as an instance profile, which grants the instance temporary credentials to access AWS resources.
4. **Providing access to your AWS resources to users that are authenticated using an external identity provider**: This is a valid use case where you can use IAM roles with identity federation to grant access to AWS resources.

 [IAM Role Management]([https://aws.amazon.com/iam/faqs/#IAM_role_management](https://aws.amazon.com/iam/faqs/#IAM_role_management "undefined"))

---

### Question

Which is a use case for deploying vCenter on AWS?

1. Cloud migration
2. Complicated disaster recovery plans
3. Decreased reliability
4. Increased costs

### Answer

A use case for deploying vCenter on AWS is:

**Cloud migration**

### Explanation

Deploying VMware vCenter on AWS, specifically through VMware Cloud on AWS, enables seamless cloud migration of on-premises VMware environments to the AWS cloud. This solution allows businesses to extend their existing vSphere environments to AWS, facilitating a hybrid cloud architecture. Key benefits include:

- **Cloud Migration**: It simplifies the process of moving workloads from on-premises data centers to the cloud, enabling organizations to scale resources dynamically.
- **Disaster Recovery**: While the option mentions "complicated disaster recovery plans," deploying vCenter on AWS actually simplifies disaster recovery by leveraging AWS's robust infrastructure, reducing complexity and improving reliability.
- **Increased Reliability**: Hosting VMware environments on AWS can enhance reliability through AWS's high availability and robust infrastructure.
- **Cost Efficiency**: Although deploying vCenter on AWS involves costs, it can lead to cost savings by eliminating the need for on-premises hardware and leveraging AWS's pay-as-you-go pricing model.

In summary, the primary use case for deploying vCenter on AWS is facilitating cloud migration, along with improving disaster recovery and enhancing overall infrastructure reliability.

---

### Question

What is user data most commonly used for?

1. For updating the content of your S3 bucket
2. For deploying EC2 instances into an Auto Scaling group
3. For bootstrapping an EC2 instance as it comes online
4. For rotating your IAM passwords

### Answer

User data is most commonly used:

**For bootstrapping an EC2 instance as it comes online.**

### Explanation

User data in AWS is a feature that allows you to pass configuration scripts or cloud-init directives to an EC2 instance at launch. These scripts are executed automatically when the instance boots for the first time. This process is known as "bootstrapping" and can include tasks such as:

- Installing software packages
- Configuring application settings
- Applying patches and updates
- Running custom initialization scripts

This functionality is essential for automating the setup and configuration of EC2 instances, ensuring they are ready to use immediately after they come online. The other options listed do not pertain to the primary use case of user data:
- **Updating the content of your S3 bucket**: This can be done through other means, such as S3 APIs or CLI commands.
- **Deploying EC2 instances into an Auto Scaling group**: While Auto Scaling groups can use user data to configure instances, the main purpose of user data itself is for bootstrapping individual instances.
- **Rotating your IAM passwords**: This is managed through IAM policies and procedures, not user data scripts.

[Run Commands on Your Linux Instance at Launch](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/user-data.html "null")

---

### Question

Which AWS service allows you to bring the power of AWS to your on-premises data center?

1. AWS Outposts
2. vCenter on AWS
3. EC2 On-Premises
4. AWS Storage Gateway

### Answer

**AWS Outposts**

### Explanation

AWS Outposts is a fully managed service that extends AWS infrastructure, services, APIs, and tools to virtually any on-premises facility. It allows you to run AWS services on-premises, enabling a truly consistent hybrid experience. This is ideal for workloads that require low latency access to on-premises systems, data residency requirements, or local data processing needs.

The other options listed are not designed to bring the full power of AWS services to an on-premises environment:
- **vCenter on AWS**: This refers to VMware Cloud on AWS, which enables VMware environments to run on AWS infrastructure but does not extend AWS services to on-premises data centers.
- **EC2 On-Premises**: This is not an actual AWS service.
- **AWS Storage Gateway**: This service connects on-premises software appliances with cloud-based storage to provide seamless integration between on-premises IT environments and AWS storage infrastructure, but it does not bring the full range of AWS services to the on-premises environment.

---

### Question

What happens when your Spot instance is chosen by AWS for termination?

1. You will get no notification and your host will be terminated without warning.
2. You will get a 10-minute notification sent to your specified email address.
3. You will get a one-hour notification sent via Amazon SNS directly to your EC2 instance on port 65.
4. While it is possible that your Spot Instance is interrupted before the warnings can be made, AWS makes a best effort to provide two-minute Spot Instance interruption notices to the metadata of your EC2 instance(s).

### Answer

**While it is possible that your Spot Instance is interrupted before the warnings can be made, AWS makes a best effort to provide two-minute Spot Instance interruption notices to the metadata of your EC2 instance(s).**

### Explanation

When AWS chooses to terminate a Spot Instance, they make a best effort to provide a two-minute warning before the instance is interrupted. This notice is made available through the instance metadata, which allows applications running on the instance to perform any necessary shutdown or data preservation tasks.

The other options are incorrect:
- **No notification**: AWS does provide notifications to the best of its ability.
- **10-minute notification sent to your email**: The notification is two minutes and provided via instance metadata, not email.
- **One-hour notification via Amazon SNS on port 65**: The notification period is two minutes, and it is not sent via SNS or to a specific port.

The correct and typical process involves the instance checking the metadata for the interruption notice, allowing it to gracefully handle the interruption.

[Spot Instance Interruption Notices](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/spot-interruptions.html#spot-instance-termination-notices "null")

---

### Question

You have a small number of critical instances that should be kept separate from each other. You want to ensure that each instance is placed on distinct underlying hardware. Which placement group would best fit this scenario?

1. High availability
2. Partition
3. Spread
4. Cluster

### Answer

**Spread**

### Explanation

A spread placement group is designed to ensure that each instance is placed on distinct underlying hardware, which reduces the risk of simultaneous failures affecting multiple instances. This makes it ideal for critical instances that need to be kept separate from each other to increase availability and fault tolerance.

The other options are not suitable for this specific requirement:
- **High availability**: This is a general concept rather than a specific AWS placement group type.
- **Partition**: Partition placement groups are designed for larger distributed and replicated workloads, where each partition can span multiple instances but still maintain fault isolation. It is more complex and typically used for larger scale-out applications.
- **Cluster**: Cluster placement groups are used to group instances close together within a single Availability Zone to achieve low-latency and high-throughput network performance, which is not suitable for keeping instances on distinct hardware for fault isolation.

 [Spread Placement Groups](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/placement-groups.html#placement-groups-spread "null")

---

### Question

Which of the following statements about user data on an EC2 instance is true?

1. If you stop an instance, modify its user data, and start the instance, the updated user data is run automatically.
2. By default, user data runs one time and one time only.
3. User data is automatically updated when you restart an instance.
4. If you reboot your EC2 instance, the user data will rerun automatically.

### Answer

**By default, user data runs one time and one time only.**

### Explanation

By default, the user data scripts or commands that you specify are executed only once when the instance is first launched. This means that they do not run automatically if you stop and start the instance, restart it, or reboot it. If you need user data to run again, you would need to configure your scripts accordingly or manually trigger them after such operations. The other statements are not true as per the default behavior of user data in EC2 instances.

---

### Question

Which of the following statements about IAM roles is true?

1. IAM roles can only be associated with one user.
2. IAM roles are associated with standard, long-term access keys and passwords for each role.
3. IAM roles can only be used to allow EC2 instances to talk to other AWS services.
4. IAM roles can be used with a variety of AWS services.

### Answer

**IAM roles can be used with a variety of AWS services.**

### Explanation

IAM roles are designed to be assumed by any entity that needs temporary credentials to access AWS services, and this includes not just users and EC2 instances, but a wide variety of AWS services such as Lambda functions, ECS tasks, CloudFormation stacks, and more. Roles provide temporary security credentials for these entities, ensuring secure and controlled access to AWS resources without requiring long-term access keys and passwords. This makes IAM roles very versatile in managing permissions and access across different AWS services and use cases. 

The other statements are not true:
- IAM roles can be assumed by multiple users, services, or applications as needed.
- IAM roles use temporary security credentials, not long-term access keys and passwords.
- While IAM roles are commonly used with EC2 instances, they are not limited to this use case alone.

---


### Question

What is user data most commonly used for?

1. For updating the content of your S3 bucket
2. For bootstrapping an EC2 instance as it comes online
3. For rotating your IAM passwords
4. For deploying EC2 instances into an Auto Scaling group

### Answer

**For bootstrapping an EC2 instance as it comes online.**

### Explanation

User data in AWS is a feature that allows you to specify configuration scripts or commands that run automatically when an EC2 instance is launched. This process is known as "bootstrapping." It is used to set up and configure the instance without manual intervention. Examples of tasks performed during bootstrapping include:

- Installing software packages
- Configuring application settings
- Running initialization scripts
- Applying patches and updates

This makes the instance ready to use immediately after it comes online. The other options listed do not pertain to the primary use case of user data:
- **Updating the content of your S3 bucket**: This is managed through other AWS services and APIs.
- **Rotating your IAM passwords**: This is handled through IAM policies and procedures, not user data scripts.
- **Deploying EC2 instances into an Auto Scaling group**: While user data can be used within Auto Scaling groups for instance configuration, the primary function of user data is for bootstrapping individual instances as they come online.