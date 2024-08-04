### Summary of RDS (Relational Database Service)

### Introduction to Relational Databases

- **Relational Databases**: Organize data into tables, similar to spreadsheets, with rows (data items) and columns (fields).
- **Tables**: Contain multiple rows and columns, storing structured data.
- **Examples of Tables**: Customer information table with columns for customer details like first name, surname, address, etc.

### RDS Database Engines

- **Available Engines**: Microsoft SQL Server, PostgreSQL, Oracle, MariaDB, MySQL, Amazon Aurora.

### Advantages of RDS

- **Easy Provisioning**: Quickly set up an RDS instance within minutes.
- **Managed Service**: No access to the underlying operating system, only to the database.
- **High Availability**: Multi-AZ (availability zone) deployment with automated failover.
- **Automated Backups**: Configurable automated backup schedules.

### OLTP vs. OLAP

- **OLTP (Online Transaction Processing)**:
    - Suitable for real-time transaction processing.
    - Examples: Banking transactions, booking systems, customer orders.
    - Uses RDS for efficient handling of large numbers of small transactions.
- **OLAP (Online Analytical Processing)**:
    - Suitable for complex queries analyzing historical data.
    - Examples: Data analysis, sales forecasting, financial modeling.
    - Uses Redshift for handling large amounts of data and complex queries.

### Multi-AZ RDS

- **Multi-AZ Setup**:
    - RDS automatically replicates the primary database to a standby database in a different availability zone.
    - Provides automated failover in case of an availability zone failure.
- **Supported Engines**: SQL Server, MySQL, MariaDB, Oracle, PostgreSQL.
- **Aurora**: Always Multi-AZ by design.
![[Pasted image 20240802160933.png]]
### Handling Failures and Maintenance

- **Automatic Failover**:
    - In case of failure, RDS automatically switches to the standby database using DNS failover.
    - No need to manually update connection strings.
- **Primary and Standby Databases**:
    - Standby is used only for failover, not for load balancing or performance improvement.

### Setting Up RDS in AWS Console

- **Creating an RDS Instance**:
    - Choose between standard create and easy create.
    - Select the database engine (e.g., MySQL).
    - Configure instance settings (e.g., Multi-AZ, storage, VPC, security groups).
    - Estimated costs provided for different configurations.

### Exam Tips

- **Database Types**: Be familiar with SQL Server, Oracle, MySQL, PostgreSQL, MariaDB, Amazon Aurora.
- **OLTP**: RDS is used for online transaction processing (OLTP).
- **OLAP**: Use Redshift for online analytical processing (OLAP) tasks like data warehousing, reporting, and sales forecasting.
- **Multi-AZ**: Understand the benefits of Multi-AZ deployments for high availability and automated failover.

By understanding these key points about RDS, you will be well-prepared for questions related to relational databases and RDS in the AWS Solutions Architect Associate exam.