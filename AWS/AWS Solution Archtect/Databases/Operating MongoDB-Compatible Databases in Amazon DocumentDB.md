### Summary: Operating MongoDB-Compatible Databases in Amazon DocumentDB

### What is MongoDB?

- **Definition**: A document database known for its scalability, flexibility, robust querying, and indexing features.
- **Example Structure**: Uses NoSQL format with key-value pairs.
    - Example for a restaurant database:
        
        ```json
        {
          "name": "Sun Bakery",
          "stars": 5,
          "categories": ["Bakery", "Breakfast"]
        },
        {
          "name": "Blue Bagels Grill",
          "stars": 4,
          "categories": ["Grill", "Bagels"]
        }
        
        ```
        

### What is Amazon DocumentDB?

- **Definition**: A managed database service on AWS that allows you to run MongoDB workloads.
- **Benefits**:
    - **Scalability**: Automatically scales with workloads.
    - **Durability**: Ensures data is stored safely.
    - **Operational Ease**: Eliminates the need for manual tasks like cluster management, configuring backups, and monitoring.

### Why Use Amazon DocumentDB?

- **No Refactoring**: Allows for seamless migration of existing MongoDB databases to AWS without changing the application code.
- **Reduced Overheads**: Reduces operational overheads by automating management tasks.

### Exam Tips

- **Migration Scenario**:
    - **Situation**: Migrating MongoDB databases from on-premises to AWS.
    - **Tool**: AWS Database Migration Service (DMS) to automate the migration process.
    - **Choice**: Use Amazon DocumentDB to run MongoDB workloads on AWS.
    - **Distractor**: DynamoDB is a NoSQL database but cannot run MongoDB workloads.

### Key Points to Remember for the Exam

- **Amazon DocumentDB**: Used specifically for running MongoDB workloads on AWS.
- **AWS Database Migration Service**: Facilitates the migration of databases from on-premises to AWS.
- **Not DynamoDB**: While both are NoSQL databases, DynamoDB cannot be used for MongoDB workloads.

