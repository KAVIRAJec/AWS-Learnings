## AWS Backup
AWS Backup is a fully managed backup service that simplifies and automates the process of backing up data across AWS services. It provides a centralized way to manage backups, ensuring compliance and data protection for various AWS resources.

### Key Concepts
- **Backup Plan**: A set of rules that define how backups are created, retained, and deleted.
- **Backup Vault**: A container for storing backups. It provides access control and encryption for backup data.
- **Backup Rule**: A set of instructions that define when and how backups are created.
- **Lifecycle Policy**: A set of rules that define how long backups are retained and when they are transitioned to cold storage or deleted.

### Supported Services
- **Amazon EC2**
- **Amazon RDS**
- **Amazon DynamoDB**
- **Amazon EFS**
- **Amazon FSx**

### Backup Storage Classes
- **Standard/warm**: For frequently accessed backups.
- **Cold**: For infrequently accessed backups, offering lower storage costs but higher retrieval costs.