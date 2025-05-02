## AWS Database Services
AWS provides a variety of database services to meet different application needs, including relational databases, NoSQL databases, in-memory databases, and data warehousing solutions. 

1. **Amazon RDS (Relational Database Service)**
2. **Amazon DynamoDB**
3. **Amazon Aurora**
4. **Amazon Redshift**
5. **Amazon ElastiCache**
6. **Amazon Neptune**
7. **Amazon DocumentDB**
8. **Amazon Timestream**
9. **Amazon Keyspaces**
10. **Amazon QLDB (Quantum Ledger Database)**

1. **Amazon RDS (Relational Database Service)**: A managed relational database service for SQL that supports multiple database engines, including MySQL, PostgreSQL, Oracle, SQL Server, and Amazon Aurora.
   - **Key Features**:
     - Automated backups for 1 to 35days(can be disabled) and restore capabilities. Manual backup using snapshot.
     - Automated provisioning of OS and patching
     - Automated scaling of storage and compute resources. You have to set maximum storage threshold. Automatically modify storage when free space is less than 10%, low storage last 5 minutes, 6 hours passed since last modification.
     - Multi-AZ deployments for high availability
     - **Read Replicas:**
        - Read replicas are used to offload read traffic from the primary database instance. They can be in the same region or in different regions to improve read performance, scalability, and availability.
        - Can create upto 15 read replicas for a single primary instance. It is used only for select statements.
        - Within AZ, cross AZ or cross region.
        - Read replicas are asynchronous and can be used for reporting and analytics workloads.
      - **Multi-AZ deployments**: Multi-AZ deployments provide high availability and failover support for RDS instances. In a Multi-AZ deployment, AWS automatically creates a synchronous standby replica in a different Availability Zone (AZ) to ensure data durability and availability but not scalability.
        - Multi-AZ is used for high availability and disaster recovery. It is synchronous and used for all types of queries.
        - Multi-AZ is not available for all database engines.
        - Read replica be setup as a Multi-AZ for disaster recovery.
        - To convert from single AZ to Multi-AZ, no need downtime(stop), just click modify and select Multi-AZ.(internally, it create a snapshot and create a new instance in another AZ and establish Sync Connection.)
     - Encryption at rest and in transit meaning **data is protected while stored and during transmission.**
     - RDS Read Replicas is free for **same region** and charged for **cross region**.
     - **RDS Custom**: A fully managed database service that allows you to run your own database engine on AWS infrastructure. It allows you to access the underlying operating system and database engine, providing more control over the database environment.
     - **RDS Proxy**: A fully managed, highly available database proxy for Amazon RDS and Amazon Aurora. It helps improve application availability and scalability by pooling and sharing database connections. It reduces the failover by 66% and keep connection active in your application. It is serverless. It is not publicly accessible(only from VPC). Ensure the secure authentication of IAM and store secrets in AWS Secrets Manager.
   - **Use Cases**:
     - Web applications, e-commerce platforms, and any application requiring a relational database.
    
2. **Amazon DynamoDB**: A fully managed NoSQL database service that provides fast and predictable performance with seamless scalability.
   - **Key Features**:
     - **Single-digit millisecond response times(High Availability by default)**
     - Global tables for multi-region replication
     - Built-in security, backup, and restore features
     - Key-value data model and Transaction support(ACID transactions) with DynamoDB transactions even in NOSQL databases.
   - **Use Cases**:
     - Mobile applications, gaming, IoT applications, and any application requiring low-latency data access.

3. **Amazon Aurora**: A MySQL and PostgreSQL-compatible relational database built for the cloud.
   - **Key Features**:
     - 5x performance improvement over MySQL and 3x over PostgreSQL.
     - Automated backups for 1 to 35days(cannot be disabled) and manual backup using snapshots. Automatically increment of 10GB upto 128TB with shared storage. For **Long term backup**, you can use **On demand backup** which is stored in S3 and can be restored to any region.
     - Upto 20% costlier than RDS.
     - Upto 15 read replicas for scaling read workloads(faster than RDS).
     - Supports Cross-region replication for disaster recovery.
     - **Availability**: Amazon Aurora provides high availability with automatic failover and replication across multiple Availability Zones.
         - It create 6 copies of data across 3 AZs. 4 out of 6 copies are needed for write operation and 3 out of 6 copies are needed for read operation. Self healing storage.
         - It automatically failover for master less than 30 seconds.
     - **DB Clusters**: There are 3 types of DB clusters endpoints in Amazon Aurora:
      - **Cluster Endpoint(Writer Endpoint)**: This endpoint is used for read and write operations. It automatically directs traffic to the primary instance of the cluster.
      - **Reader Endpoint**: This endpoint is used for read operations only. It automatically distributes read traffic across all available read replicas in the cluster. When CPU utilization is high(load), it automatically add read replicas to the cluster.
      - **Custom Endpoint**: This endpoint allows you to create a custom endpoint that can be used to route traffic to specific instances/replica in the cluster. It is useful for applications that require specific routing logic or for testing purposes especially for analytics workloads.
    - **Serverless**: Amazon Aurora Serverless is an on-demand, auto-scaling configuration for Aurora that automatically starts up, shuts down, and scales the database based on your application's needs.
      - It is used for infrequent workloads and unpredictable workloads.
      - It automatically scales instances and they are accessed through the proxy endpoint which is managed by AWS.
      - It is not available for all database engines.
      - It is not available for cross-region replication.
    - **Global Database**: Amazon Aurora Global Database is a feature that allows you to create a single Aurora database that spans multiple AWS regions. It provides low-latency (< 1 second) global reads and disaster recovery capabilities.
      - It is used for cross-region replication and disaster recovery.
      - It is not available for all database engines.
      - It is not available for serverless.
    - **Aurora Machine Learning**: Amazon Aurora Machine Learning is a feature that allows you to integrate machine learning capabilities into your Aurora database. It enables you to run machine learning models directly within the database using SQL queries.
    - **BabelFish**: Amazon Aurora supports the **BabelFish** feature, which allows you to run SQL Server applications on Aurora PostgreSQL without code changes. It provides compatibility with SQL Server syntax and features, making it easier to migrate applications from SQL Server to Aurora PostgreSQL.
    - **Aurora Database Cloning**: Amazon Aurora supports database cloning, which allows you to create a copy of an Aurora database cluster without duplicating the underlying storage. This feature is useful for creating test environments or for development purposes.

   - **Use Cases**:
     - High-performance applications, SaaS applications, and any application requiring a relational database with high availability.

- **RDS & Aurora Encryption**: They are not encrypted by default. You have to enable encryption at rest and in transit. Encryption at rest is done using AWS Key Management Service (KMS). Cannot encrypt the read replicas unless primary instance is encrypted. To encrypt unencrypted instance, create snapshot and encrypt while restoring. 

4. **Amazon Redshift**: A fully managed, petabyte-scale data warehouse service designed for online analytical processing (OLAP) and complex queries.
   - **Key Features**:
     - Columnar storage for efficient querying
     - Massively parallel processing (MPP) architecture
     - Integration with AWS Glue for ETL processes
   - **Use Cases**:
     - Business intelligence, data analytics, and reporting.

5. **Amazon ElastiCache**: A fully managed in-memory caching service that supports **Redis and Memcached**, designed to improve application performance by caching frequently accessed data.
   - **Key Features**:
     - Sub-millisecond response times
     - Make application stateless by storing session data in ElastiCache.
     - AWS take care of the underlying infrastructure, including hardware provisioning, software patching, and monitoring.
     - **Redis**: Supports data structures such as strings, hashes, lists, sets, and sorted sets. Multi AZ replication with auto failover, read replicas for high availability. Data persistence options (RDB and AOF) for durability. Backup and restore capabilities. Support IAM Authentication(for AWS level) and Redis Auth(for Redis level). Used for real time ranking, leaderboards, and caching(sorted sets)
     - **Memcached**: A simple key-value store with no data persistence options. Multi node for data partitioning(data sharing). No high availability. Backup and restore capabilities.
   - **Pattern for Caching**:
     - **Lazy Loading**: Data is loaded into the cache only when it is requested for the first time. Subsequent requests retrieve the data from the cache.
      - **Write-Through**: Data is written to both the cache and the underlying data store simultaneously. This ensures that the cache is always up to date with the latest data.
      - **Session Store**: ElastiCache can be used to store session data for web applications, allowing for fast access to user sessions and reducing the load on the primary database.
   - **Use Cases**:
     - Caching, session management, and real-time analytics.

6. **Amazon Neptune**: A fully managed graph database service that supports both property graph and RDF graph models.
   - **Key Features**:
     - Supports Gremlin and SPARQL query languages
     - High availability and durability
     - Integration with AWS Identity and Access Management (IAM)
   - **Use Cases**:
     - Social networking, recommendation engines, and fraud detection.

7. **Amazon DocumentDB**: A fully managed document database service that is compatible with MongoDB.
   - **Key Features**:
     - Fully managed and scalable
     - Supports MongoDB APIs and tools
     - Automated backups and scaling
     - Used for JSON-like documents (migration from MongoDB to DocumentDB)
   - **Use Cases**:
     - Content management systems, catalogs, and any application requiring a document database.

8. **Amazon Timestream**: A fully managed time series database service designed for IoT and operational applications.
   - **Key Features**:
     - Built-in time series data management
     - Serverless and scalable
     - Integration with AWS IoT Core and Amazon QuickSight
   - **Use Cases**:
     - IoT applications, operational monitoring, and analytics.

9. **Amazon Keyspaces**: A fully managed Apache Cassandra-compatible database service.
   - **Key Features**:
     - Serverless and scalable
     - Supports Cassandra Query Language (CQL)
     - Integrated with AWS Identity and Access Management (IAM)
   - **Use Cases**:
     - IoT applications, mobile backends, and any application requiring a NoSQL database.

10. **Amazon QLDB (Quantum Ledger Database)**: A fully managed ledger database that provides a transparent, immutable, and cryptographically verifiable transaction log.
   - **Key Features**:
     - Built-in cryptographic verification
     - Serverless and scalable
     - Supports PartiQL query language
   - **Use Cases**:
     - Supply chain tracking, financial transactions, and any application requiring an immutable ledger.