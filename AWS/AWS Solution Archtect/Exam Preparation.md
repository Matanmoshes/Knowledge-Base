# Questions:

### Question

You have joined a newly formed software company as a Solutions Architect. It is a small company, and you are the only employee with AWS experience. The owner has asked for your recommendations to ensure that the AWS resources are deployed to proactively remain within budget. Which AWS service can you use to help ensure you don’t have cost overruns for your AWS resources?

1. Billing and Cost Management
2. Inspector
3. Cost Explorer
4. AWS Budgets

### Answer

**AWS Budgets**

### Explanation

AWS Budgets allows you to set custom cost and usage budgets and receive notifications when your usage or spending exceeds or is forecasted to exceed the thresholds you set. This service provides proactive alerts, enabling you to manage your AWS costs effectively and avoid overruns.

The other options are not specifically designed to proactively manage and prevent cost overruns:

- **Billing and Cost Management**: Provides detailed billing information but is more reactive rather than proactive.
- **Inspector**: A security assessment service that helps improve the security and compliance of applications deployed on AWS, not related to cost management.
- **Cost Explorer**: Allows you to visualize and analyze your costs and usage, which is useful for historical data and trends but does not provide proactive budget management features like AWS Budgets.

---

### Question

You have been given an assignment to configure Network ACLs in your VPC. Before configuring the NACLs, you need to understand how the NACLs are evaluated. How are NACL rules evaluated?

1. All NACL rules that you configure are evaluated before traffic is passed through.
2. NACL rules are evaluated by rule number from highest to lowest, and all are evaluated before traffic is passed through.
3. NACL rules are evaluated by rule number from highest to lowest, and executed immediately when a matching rule is found.
4. NACL rules are evaluated by rule number from lowest to highest and executed immediately when a matching rule is found.

### Answer

**NACL rules are evaluated by rule number from lowest to highest and executed immediately when a matching rule is found.**

### Explanation

Network ACLs (NACLs) in AWS are stateless, meaning that they evaluate each rule independently and do not keep track of the state of a connection. NACL rules are evaluated in ascending order by the rule number (lowest to highest). As soon as a matching rule is found, it is applied immediately, and no further rules are evaluated. This allows for efficient traffic filtering and control within your VPC.

---

### Question

Your company has decided to migrate a SQL Server database to a newly-created AWS account. Which service can be used to migrate the database?

1. DynamoDB
2. Database Migration Service
3. Elasticache
4. AWS RDS

### Answer

**Database Migration Service**

### Explanation

AWS Database Migration Service (DMS) is specifically designed to help you migrate databases to AWS easily and securely. DMS supports homogeneous migrations (e.g., SQL Server to SQL Server) as well as heterogeneous migrations (e.g., SQL Server to Amazon Aurora). It can handle the migration with minimal downtime and supports a wide variety of database engines, including SQL Server.

The other options are not suitable for this specific use case:

- **DynamoDB**: A NoSQL database service, not suitable for directly migrating a SQL Server database.
- **Elasticache**: A caching service, not designed for database migrations.
- **AWS RDS**: A managed database service that can host SQL Server databases, but it is not a migration tool. RDS can be the target of the migration, but the actual migration process should be handled by DMS.

---

### Question

You have been evaluating the NACLs in your company. Most of the NACLs are configured the same:

```bash
100 All Traffic Allow
200 All Traffic Deny
* All Traffic Deny
```

If a request comes in, how will it be evaluated?

1. The default will deny traffic.
2. All rules will be evaluated and the end result will be Deny.
3. The request will be allowed.
4. The highest numbered rule will be used, a deny.

### Answer

**The request will be allowed.**

### Explanation

Network ACL (NACL) rules are evaluated in order by their rule number, from lowest to highest. The evaluation stops as soon as a rule matches the traffic. In this case:

- Rule 100 allows all traffic.
- Rule 200 denies all traffic.
- The default rule (indicated by ``) denies all traffic.

Since Rule 100 is the first rule and it allows all traffic, the request will be allowed, and no further rules will be evaluated. Therefore, the correct answer is that the request will be allowed.

---

### Question

A team of architects is designing a new AWS environment for a company which wants to migrate to the Cloud. The architects are considering the use of EC2 instances with instance store volumes. The architects realize that the data on the instance store volumes are ephemeral. Which action will not cause the data to be deleted on an instance store volume?

1. The underlying disk drive fails.
2. Instance is stopped.
3. Hardware disk failure.
4. Reboot.

### Answer

**Reboot**

### Explanation

Data stored on instance store volumes is ephemeral, meaning it persists only as long as the instance is running. The data will be lost in the following situations:

- **The underlying disk drive fails.**
- **Instance is stopped.**
- **Hardware disk failure.**

However, **rebooting the instance** does not affect the data on instance store volumes. The data remains intact across reboots, which is why the correct answer is "Reboot."

---

### Question

You have recently migrated your small company to AWS and are looking for some general best practice guidance within the platform. Which AWS service can help you optimize your AWS environment by giving recommendations to reduce cost, increase performance, and improve security?

1. AWS Trusted Advisor
2. AWS Organizations
3. AWS Inspector
4. AWS Optimizations

### Answer

**AWS Trusted Advisor**

### Explanation

AWS Trusted Advisor is a service that provides real-time guidance to help you provision your resources following AWS best practices. It offers recommendations to optimize your AWS environment in five categories: cost optimization, performance, security, fault tolerance, and service limits. This service can help you reduce costs, increase performance, and improve security, making it an ideal choice for optimizing your AWS environment.

The other options do not provide the same level of comprehensive best practice guidance:

- **AWS Organizations**: Helps you centrally manage and govern your environment as you grow and scale your AWS resources across multiple AWS accounts, but it is not focused on optimization recommendations.
- **AWS Inspector**: An automated security assessment service that helps improve the security and compliance of applications deployed on AWS.
- **AWS Optimizations**: This is not an actual AWS service.

---

### Question

You have recently migrated your small company to AWS and are looking for some general best practice guidance within the platform. Which AWS service can help you optimize your AWS environment by giving recommendations to reduce cost, increase performance, and improve security?

1. AWS Trusted Advisor
2. AWS Organizations
3. AWS Inspector
4. AWS Optimizations

### Answer

**AWS Trusted Advisor**

### Explanation

AWS Trusted Advisor is a service that provides real-time guidance to help you provision your resources following AWS best practices. It offers recommendations to optimize your AWS environment in five categories: cost optimization, performance, security, fault tolerance, and service limits. This service can help you reduce costs, increase performance, and improve security, making it an ideal choice for optimizing your AWS environment.

The other options do not provide the same level of comprehensive best practice guidance:

- **AWS Organizations**: Helps you centrally manage and govern your environment as you grow and scale your AWS resources across multiple AWS accounts, but it is not focused on optimization recommendations.
- **AWS Inspector**: An automated security assessment service that helps improve the security and compliance of applications deployed on AWS.
- **AWS Optimizations**: This is not an actual AWS service.

---

### Question

Your company is in the process of creating a multi-region disaster recovery solution for your database, and you have been tasked to implement it. The required RTO is 1 hour, and the RPO is 15 minutes. What steps can you take to ensure these thresholds are met?

1. Use RDS to host your database. Create a cross-region read replica of your database. In the event of a failure, promote the read replica to be a standalone database. Send new reads and writes to this database.
2. Take EBS snapshots of the required EC2 instances nightly. In the event of a disaster, restore the snapshots to another region.
3. Use Redshift to host your database. Enable Multi-AZ deployment for high availability within a region. In the event of an AZ failure, Redshift will automatically handle failover. For disaster recovery, implement a separate cross-region replication strategy.
4. Use RDS to host your database. Enable the Multi-AZ option for your database. In the event of a failure, cut over to the secondary database.

### Answer

**Use RDS to host your database. Create a cross-region read replica of your database. In the event of a failure, promote the read replica to be a standalone database. Send new reads and writes to this database.**

### Explanation

To meet the required RTO (Recovery Time Objective) of 1 hour and RPO (Recovery Point Objective) of 15 minutes, the most suitable option is to use Amazon RDS with cross-region read replicas. This setup ensures that data is asynchronously replicated across regions, providing a backup that can be quickly promoted to a standalone database in case of a disaster. This meets the 1-hour RTO by allowing relatively fast promotion and switchover, and the 15-minute RPO by maintaining frequent replication updates.

The other options do not meet both the RTO and RPO requirements:

- **Taking EBS snapshots nightly**: This does not meet the RPO of 15 minutes, as data loss could be up to 24 hours.
- **Using Redshift with Multi-AZ deployment**: This solution provides high availability within a region but requires a separate cross-region replication strategy, which is not specified here and may not meet the RTO and RPO requirements.
- **Using RDS with Multi-AZ**: This provides high availability within a single region but does not address multi-region disaster recovery, thus failing to meet the requirements for cross-region disaster recovery.

---

### Question

A small startup company has begun using AWS for all of its IT infrastructure. The company has one AWS Solutions Architect and the demands for their time are overwhelming. The software team has been given permission to deploy their Python and PHP applications on their own. They would like to deploy these applications without having to worry about the underlying infrastructure. Which AWS service would they use for deployments?

1. Elastic Beanstalk
2. CodeDeploy
3. CloudFront
4. CloudFormation

### Answer

**Elastic Beanstalk**

### Explanation

AWS Elastic Beanstalk is a fully managed service that makes it easy to deploy and run applications and services. It abstracts away the underlying infrastructure, allowing developers to focus on writing code and deploying applications without worrying about the details of the infrastructure. Elastic Beanstalk supports several programming languages, including Python and PHP, and automatically handles the deployment, capacity provisioning, load balancing, and scaling.

The other services mentioned do not provide the same level of abstraction and ease of deployment:

- **CodeDeploy**: A deployment service that automates software deployments to a variety of compute services such as EC2, Fargate, and Lambda, but it does not abstract the infrastructure like Elastic Beanstalk does.
- **CloudFront**: A content delivery network (CDN) service that speeds up the distribution of static and dynamic web content, but it is not used for deploying applications.
- **CloudFormation**: A service that provides infrastructure as code, allowing you to model and set up your AWS resources, but it requires a deeper understanding of the underlying infrastructure compared to Elastic Beanstalk.

---

### Question

An insurance company is creating an application that will perform analytics in near real-time on huge datasets in the terabyte range, and potentially even petabyte. The company is evaluating an AWS data storage option. Which AWS service will allow storage of petabyte-scale data and also allow fast complex queries over a large number of rows?

1. DynamoDB
2. ElastiCache
3. Redshift
4. RDS

### Answer

**Redshift**

### Explanation

Amazon Redshift is a fully managed data warehouse service that is designed to handle petabyte-scale data and perform complex queries quickly. It is optimized for large-scale data analytics, making it ideal for use cases that involve processing and analyzing massive datasets. Redshift uses columnar storage, data compression, and massively parallel processing (MPP) to deliver fast query performance over large volumes of data.

The other options are not suitable for this use case:

- **DynamoDB**: A NoSQL database service that is highly scalable and provides low-latency performance but is not optimized for complex queries over large datasets.
- **ElastiCache**: A caching service that improves the performance of web applications by retrieving data from in-memory caches, but it is not designed for storage and querying of large datasets.
- **RDS**: A managed relational database service that supports multiple database engines, but it is not optimized for the petabyte-scale data analytics required in this scenario. Redshift is specifically built for data warehousing and analytics at this scale.

---

### Question

Jennifer is a cloud engineer for her application team. The application leverages several third-party SaaS vendors to complete their workflows within the application. Currently, the team uses numerous AWS Lambda functions for each SaaS vendor that run daily to connect to the configured vendor. The functions initiate a transfer of data files, ranging from one megabyte up to 80 gibibytes in size. These data files are stored in an Amazon S3 bucket and then referenced by the application itself. The data transfer routinely fails due to execution timeout limits in the Lambda functions, and the team wants to find a simpler and less error-prone way of transferring the required data. Which solution or AWS service could be the best fit for their solution?

1. Amazon EC2 Auto Scaling Groups
2. Increase the Lambda function timeouts to one hour
3. Amazon EKS with Auto Scaling
4. Amazon AppFlow

### Answer

**Amazon AppFlow**

### Explanation

Amazon AppFlow is a fully managed integration service that makes it easy to securely transfer data between AWS services and third-party SaaS applications such as Salesforce, ServiceNow, and others. It can handle large data transfers and supports data transformation and validation. AppFlow is designed to simplify data transfers without the need for managing custom code or worrying about execution timeouts, making it an ideal solution for this scenario where Lambda functions are currently failing due to timeout limits.

The other options are less suitable for this use case:

- **Amazon EC2 Auto Scaling Groups**: While this can provide scalable compute resources, it would require more management and custom code to handle data transfers.
- **Increase the Lambda function timeouts to one hour**: This may help but does not address the root cause of complexity and error-prone data transfers. Lambda also has a maximum timeout limit of 15 minutes, so increasing it to one hour is not feasible.
- **Amazon EKS with Auto Scaling**: This would provide a scalable containerized environment but requires significant setup and management, and may be overkill for simply transferring data between SaaS vendors and S3.

---

### Question

You have just started working at a company that is migrating from a physical data center into AWS. Currently, you have 25 TB of data that needs to be moved to an S3 bucket. Your company has just finished setting up a 1 GB Direct Connect drop, but you will not have a VPN up and running for 30 days. This data needs to be encrypted during transit and at rest and must be uploaded to the S3 bucket within 21 days. What is the best way to meet these requirements?

1. Upload the data to S3 using your public internet connection.
2. Order a Snowcone device to transmit the data.
3. Upload the data using Direct Connect.
4. Use a Snowball device to transmit the data.

### Answer

**Use a Snowball device to transmit the data.**

### Explanation

AWS Snowball is a petabyte-scale data transport solution that uses secure appliances to transfer large amounts of data into and out of AWS. Snowball devices are designed to move data securely and efficiently, and they are capable of handling large datasets, such as your 25 TB of data, within the required timeframe.

- **Upload the data to S3 using your public internet connection**: This may not be practical due to potential bandwidth limitations and the time it would take to transfer 25 TB of data.
- **Order a Snowcone device to transmit the data**: Snowcone devices are smaller and intended for smaller amounts of data. They may not be suitable for transferring 25 TB efficiently.
- **Upload the data using Direct Connect**: While Direct Connect provides a dedicated connection, the absence of a VPN for 30 days might present security challenges for encrypting the data during transit. Additionally, the setup time and potential bandwidth constraints might not meet your 21-day deadline.
- **Use a Snowball device to transmit the data**: Snowball devices are specifically designed for large data transfers and come with built-in encryption, ensuring data security during transit and at rest. This method is reliable and will meet your timeline and security requirements effectively.

---

### Question

Your company has performed a Disaster Recovery drill which failed to meet the Recovery Time Objective (RTO) desired by executive management. The failure was due in large part to the amount of time taken to restore proper functioning on the database side. You have given management a recommendation of implementing synchronous data replication for the RDS database to help meet the RTO. Which of these options can perform synchronous data replication in RDS?

1. RDS Multi-AZ
2. DAX
3. Read replicas
4. AWS Database Migration Service

### Answer

**RDS Multi-AZ**

### Explanation

Amazon RDS Multi-AZ (Multi-Availability Zone) deployments provide enhanced availability and durability for RDS instances, making them suitable for production workloads. In a Multi-AZ deployment, Amazon RDS automatically provisions and maintains a synchronous standby replica in a different Availability Zone. This replication is synchronous, meaning that updates to your primary database instance are automatically replicated to the standby instance to ensure high availability. In the event of a failure or disaster, Amazon RDS can automatically fail over to the standby instance, thus minimizing downtime and helping meet the desired Recovery Time Objective (RTO).

The other options do not perform synchronous data replication:

- **DAX** (DynamoDB Accelerator) is a caching service specifically for DynamoDB, not related to RDS or synchronous replication.
- **Read replicas** in RDS use asynchronous replication, which does not guarantee that the replica will be fully up-to-date with the primary instance at any given moment.
- **AWS Database Migration Service (DMS)** is used for migrating databases to AWS and can support both homogeneous and heterogeneous migrations, but it is not typically used for real-time synchronous data replication.

---

### Question

A new startup company decides to use AWS to host their web application. They configure a VPC as well as two subnets within the VPC. They also attach an internet gateway to the VPC. In the first subnet, they create the EC2 instance which will host their web application. They finish the configuration by making the application accessible from the Internet. The second subnet hosts their database and they don’t want the database accessible from the Internet. Which statement best describes this scenario?

1. The web server is in a private subnet, and the database server is in a public subnet. The public subnet has a route to the internet gateway in the route table.
2. The web server is in a public subnet, and the database server is in a public subnet. The public subnet has a route to the internet gateway in the route table.
3. The web server is in a public subnet, and the database server is in a private subnet. The public subnet has a route to the internet gateway in the route table.
4. The web server is in a private subnet, and the database server is in a private subnet. A third subnet has a route to the Internet Gateway, which allows internet access.

### Answer

**The web server is in a public subnet, and the database server is in a private subnet. The public subnet has a route to the internet gateway in the route table.**

### Explanation

In this scenario:

- The web server needs to be accessible from the Internet, which requires it to be in a public subnet. A public subnet is a subnet that has a route to the internet gateway.
- The database server should not be accessible from the Internet, which means it should be placed in a private subnet. A private subnet does not have a route to the internet gateway.

Therefore, the correct configuration is having the web server in a public subnet with a route to the internet gateway, and the database server in a private subnet.

---

### Question

A financial institution has an application that produces huge amounts of actuary data, which is ultimately expected to be in the terabyte range. There is a need to run complex analytic queries against terabytes of structured data, using sophisticated query optimization, columnar storage on high-performance storage, and massively parallel query execution. Which service will best meet this requirement?

1. Elasticache
2. DynamoDB
3. Redshift
4. RDS

### Answer

**Redshift**

### Explanation

Amazon Redshift is a fully managed data warehouse service designed for running complex analytic queries against large volumes of structured data. It supports sophisticated query optimization, columnar storage, and massively parallel query execution, making it ideal for handling terabyte-scale data analytics.

- **Elasticache**: This service is used for in-memory caching to accelerate database queries but is not designed for handling complex analytics on large datasets.
- **DynamoDB**: This is a NoSQL database service, suitable for fast and scalable data access but not optimized for complex analytic queries on structured data.
- **RDS**: This is a managed relational database service, suitable for OLTP workloads but not specifically optimized for large-scale data analytics like Redshift.

Amazon Redshift provides the necessary features and performance for analyzing large datasets, making it the best fit for this requirement.

---

### Question

You work for a Defense contracting company. The company develops software applications which perform intensive calculations in the area of Mechanical Engineering related to metals for ship building. You have a 3-year contract and decide to purchase reserved EC2 instances for a 3-year duration. You are informed that the particular program has been cancelled abruptly and negotiations have brought the contract to an amicable conclusion one year early. What can you do to stop incurring charges and save money on the EC2 instances?

1. Convert the instances to Spot Instances and allow them to go away through attrition.
2. Write to AWS and ask to terminate the contract.
3. Sell the reserved instances on the Reserved Instance Marketplace.
4. Change the instance states from running to stopped.

### Answer

**Sell the reserved instances on the Reserved Instance Marketplace.**

### Explanation

AWS provides a Reserved Instance Marketplace where you can sell your unused Reserved Instances if you no longer need them. This can help you recoup some of your costs. Simply stopping the instances or converting them to Spot Instances will not stop the charges for the reserved capacity you have committed to. Writing to AWS to terminate the contract is generally not an option, as Reserved Instances are a commitment made for a discounted rate over a period of time.

- **Convert the instances to Spot Instances**: This option is not feasible, as you cannot directly convert Reserved Instances to Spot Instances. Additionally, Spot Instances are used for entirely different pricing models and purposes.
- **Write to AWS and ask to terminate the contract**: AWS does not typically allow early termination of Reserved Instance contracts since these are commitments made to get a lower price.
- **Change the instance states from running to stopped**: This will not stop the charges for the reserved capacity; you will still be billed for the Reserved Instances even if the instances are stopped.

Selling the Reserved Instances on the Reserved Instance Marketplace is the best way to mitigate financial loss in this scenario.

---

### Question

You work for an advertising company that has a real-time bidding application. You are also using CloudFront on the front end to accommodate a worldwide user base. Your users begin complaining about response times and pauses in real-time bidding. What is the best service that can be used to reduce DynamoDB response times by an order of magnitude (milliseconds to microseconds)?

1. DAX
2. DynamoDB Auto Scaling
3. CloudFront Edge Caches
4. ElastiCache

### Answer

**DAX**

### Explanation

Amazon DynamoDB Accelerator (DAX) is a fully managed, highly available, in-memory cache for DynamoDB that delivers up to a 10x read performance improvement—from milliseconds to microseconds—even at millions of requests per second. DAX is designed to be seamlessly integrated with your DynamoDB tables and provides fast, in-memory caching for read-heavy and bursty workloads.

The other options do not address reducing DynamoDB response times specifically:

- **DynamoDB Auto Scaling**: Helps to automatically adjust the read and write throughput capacity of your DynamoDB tables to accommodate changing traffic patterns, but it doesn't reduce response times to microseconds.
- **CloudFront Edge Caches**: Used for caching and delivering content to users globally, but it doesn't directly impact DynamoDB response times.
- **ElastiCache**: Provides in-memory caching for other databases and services but is not optimized for direct integration with DynamoDB like DAX is.

Therefore, using **DAX** is the best service to reduce DynamoDB response times significantly.

---

### Question

You are a solutions architect working for an online gaming company. The company wants to integrate Amazon, Facebook, and Google authentication into the application so people can use their existing social media accounts to sign up to the application. Which AWS service facilitates this?

1. Amazon Cognito
2. AWS Artifact
3. Amazon Detective
4. AWS Trusted Advisor

### Answer

**Amazon Cognito**

### Explanation

Amazon Cognito is an AWS service that facilitates the integration of social identity providers like Amazon, Facebook, and Google for authentication in your applications. Cognito allows you to add user sign-up, sign-in, and access control to your web and mobile apps quickly and easily. It supports various identity providers, enabling users to sign in using their existing social media accounts.

The other options do not provide the required functionality:

- **AWS Artifact**: Provides access to AWS compliance reports and agreements, but it is not related to user authentication.
- **Amazon Detective**: Helps analyze, investigate, and quickly identify the root cause of potential security issues or suspicious activities, but it does not handle user authentication.
- **AWS Trusted Advisor**: Provides real-time guidance to help you provision your resources following AWS best practices, but it does not offer authentication services.

Therefore, **Amazon Cognito** is the correct service to integrate Amazon, Facebook, and Google authentication into the application.

---