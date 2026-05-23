## AWS S3(Simple Storage Service)

AWS S3 is an object storage service that offers high durability, availability, scalability and highly available. It is designed to store and retrieve any amount of data from anywhere on the web. S3 is commonly used for backup, archiving, big data analytics, and content distribution.

**Key Concepts:**
- **Bucket**: A container for storing objects. Each bucket has a globally unique name across all AWS accounts. Buckets are regionally scoped, meaning they are created in a specific AWS region.
- **Object**: The fundamental entity stored in S3, consisting of data, metadata, and a unique identifier (key). Things like images, videos, and documents are stored as objects in S3.
   - **Maximum object size**: 5 TB (if above 5TB, must use multipart upload)
- **Key**: The unique identifier for an object within a bucket. It is the full path to the object, including the file name and any prefixes (folders). Key is composed of prefix and object name(example: `s3://my-bucket/prefix/object_name`). There is no concept of directories in S3, but you can use prefixes to simulate a folder like structure in UI in S3 console.
- **Metadata**: Additional information about an object, such as content type, size, and custom metadata. It is a list of key-value pairs that provide information about the object used for system and user-defined metadata.
- **Versioning**: A feature that allows you to keep multiple versions of an object in a bucket. This is useful for data recovery and rollback purposes.
   - If no versioning is enabled, the object will have a version ID of "null".
   - Deleting any object while versioning enabled will create a delete marker. This helps to recover the deleted object by removing the delete marker.
   - Suspends versioning: stops creating new versions but retains existing ones.
- **Lifecycle Policies**: Rules that automate the transition of objects between storage classes or delete them after a specified period. This can created in a specific prefix or in particular tags. It is useful for cost optimization and data management.
   - **Transition**: Move objects to a different storage class after a specified period.
   - **Expiration**: Permanently delete objects after a specified period and Incomplete multipart uploads.
   - **Noncurrent Version Expiration**: Permanently delete noncurrent versions of objects after a specified period.
   - **Incomplete Multipart Upload**: Automatically delete incomplete multipart uploads after a specified period.
- **Static website hosting**: S3 can be used to host static websites by serving HTML, CSS, and JavaScript files directly from a bucket. **For static website hosting, the bucket should be public accessible.**
- **Replication**: 
   - **Intro**
      - S3 supports replication features to automatically copy objects between buckets in the same or different regions. 
      - To enable replication, **the versioning must be enabled**. 
      - The bucket can be in different AWS Accounts. We can enable replication for a particular prefix or all objects. 
      - These operations are asynchronous. Also, must provide a proper IAM permissions. 
   - **Cross-Region Replication (CRR)**: Automatically replicates objects from one bucket to another bucket in a different region. Use case: Compliance, low latency access & replication across accounts.
   - **Same-Region Replication (SRR)**: Automatically replicates objects within the same region. Use case: low aggregation, live replication between test & production accounts.
   - **Replication Time Control (RTC)**: Provides a predictable replication time for CRR and SRR.
   - **S3 Batch Replication**:
      - After you enabled replication, only new objects are replicated. If you want to replicate existing objects, you can use S3 Batch Replication to replicate a large number of objects in a single operation. 
      - For delete, it only replicates delete markers(if enabled), not objects with version ID(if particular version is deleted, which means permanent delete which are not replicated). 
      - There is no chaining of replication(i.e. if you have a bucket A and B, and you replicate A to B, and then replicate B to C, it will not replicate A to C).
   - **S3 Replication Encryption**: 
      - Unencrypted objects, SSE-S3, SSE-C are replicated by default. For SSE-KMS, you must specify the destination KMS key for replication.
      - For SSE-KMS, you need to enable it by defining KMS key in target bucket(having proper key policies & IAM Role - KMS decrypt in source bucket and KMS encrypt in target bucket) and then enable replication configuration with KMS encryption.
      - If you use multi region KMS keys, AWS S3 will consider them as independent keys(so object will still be decrypted and encrypted with the same key in the destination bucket, but it will be different key in the destination bucket).
- **Security**:
   - **User Based**: IAM policies that define permissions for users and groups to access S3 resources.
   - **Resource Based**:
      - **Bucket Policies**: JSON-based policies that define permissions for a bucket and its objects. Bucket policies can grant or deny access to specific users, groups, or AWS accounts(cross account). **IAM takes precedence over bucket policies**.
      - **Access Control Lists (ACLs)**: A legacy method of managing access to S3 resources. ACLs can be applied to buckets and objects to grant permissions to specific AWS accounts or groups.(can be disabled)
      - **Object Access Control Lists (ACLs)**: Similar to ACLs, but applied to individual objects. OACLs can be used to grant permissions to specific AWS accounts or groups.(less common and can be disabled)
   - **NOTE**: an IAM principal can access S3 object if,
      - IAM User Permissions allowed OR Resource Policy allowed.
      - AND There is no explicit deny in either policy.
- **Gateway Endpoints**: S3 supports VPC endpoints, allowing you to access S3 without going through the public internet through Internet Gateway. This is useful to access S3 from private subnets securely.
- **Blocked Public Access**: By default, S3 blocks public access to buckets and objects(Additional layer of security). You can configure bucket policies or ACLs to allow public access if needed. Even while creating bucket you turned off block public access, it will still be blocked by default. You need to explicitly allow public access using bucket policies.
- **S3TA(S3 Transfer Acceleration)**: A feature that speeds up the upload and download of files from S3 bucket to clients by using Amazon CloudFront's globally distributed edge locations. It is useful for transferring large files over long distances.
- **Sample Bucket Policy**:
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::example-bucket/*"
    }
  ]
}
```
**Storage Classes:** (99.999999999%(11 9's) durability for all classes)
- **S3 Standard**: General-purpose storage for frequently accessed data. It offers high durability and availability. 99.99% availability, not available 53 minutes in a year.
   - **Use case**: Websites, content distribution, and big data analytics.
- **S3 Intelligent-Tiering**: Automatically moves data between two access tiers (frequent and infrequent) based on usage. Small monthly monitoring and auto tiering fee. No retrieval charges. The types can used:
   - **Frequent Access**(automatic): Default tier.
   - **Infrequent Access**(automatic): Not accessed for 30 days.
   - **Archive Instant Access**(automatic): Not accessed for 90 days.
   - **Archive Access**(optional): Configured from 90days to 700+ days.
   - **Archive Deep Access**(optional): Configured from 180+ days to 700+ days.
   - **Use case**: Data with unpredictable access patterns, such as machine learning data and analytics.
- **S3 Standard-IA (Infrequent Access)**: Lower-cost storage for infrequently accessed data. Data is less frequently accessed but rapid retrieval when needed. Minimum storage duration of 30 days. It has a retrieval fee but is suitable for long-term storage. 99.9% availability, not available 4 days in a year. 
   - **Use case**: Disaster recovery, backups, and long-term storage.
- **S3 One Zone-IA (Infrequent Access)**: Similar to Standard-IA but stored in a single availability zone. Data loss when AZ is destroyed. It is cheaper but less durable. Minimum storage duration of 30 days. 99.5% availability, not available 18 days in a year.
   - **Use case**: Secondary backup copies of on-premises data, and data that can be easily recreated.
- **S3 Express One Zone**:
   - A new storage class that provides the lowest cost & high performance(single digit milliseconds latency) in a single availability zone. It is designed for data that can be easily recreated and does not require high durability or availability. Minimum storage duration of 30 days. 99.5% availability, not available 18 days in a year.
   - **Use case**: Latency sensitive apps, data intensive workloads, and data that can be easily recreated.
- **S3 Glacier**: Low-cost storage for archival data with retrieval times ranging from minutes to hours. It is suitable for long-term data retention. Pricing for storage and retrieval.
   - **S3 Glacier Instant Retrieval**: Millisecond retrieval, designed for data that is accessed frequently but stored in Glacier. Minimum 90 days storage duration.
      - **Use case**: Data that is accessed frequently but needs to be stored at a lower cost.
   - **S3 Glacier Flexible Retrieval(formerly S3 Glacier)**: Expedited (1-5 minutes), Standard (3-5 hours), and Bulk (5-12 hours) retrieval options. It is free and 90 days minimum storage duration.
      - **Use case**: Long-term archival data, backups, and compliance data.
   - **S3 Glacier Deep Archive**: Lowest-cost storage for long-term archival data. Standard(12 hours), Bulk(48 hours). 180 days minimum storage duration.

- **S3 Reduced Redundancy Storage (RRS)(Deprecated)**: Provides lower durability than Standard but at a lower cost. It is suitable for non-critical data that can be easily reproduced.
- **S3 Analytics**: A feature that provides recommendation for S3 Standard and Standard-IA(not for Glacier & one-zone) usage and access patterns. It helps you understand how your data is being used and can help optimize costs. It works only for Standard and Standard-IA classes. 

![alt text](image.png)
![alt text](image-1.png)
![Storage class ordering](https://docs.aws.amazon.com/images/AmazonS3/latest/userguide/images/lifecycle-transitions-v4.png)

## Pricing:
- **Storage Costs**: Charged based on the amount of data stored in S3, measured in GB per month.
- **Data Transfer Costs**: Charged based on the amount of data transferred out of S3 to the internet or other AWS regions.

- **Requests and Retrieval Costs**: Charged based on the number of requests made to S3 (PUT, GET, LIST, etc.) and the retrieval of data from different storage classes.
- **Data Transfer In Costs**: No pricing for Data Transfer In bound & between other services within same region, but Data Transfer Out is charged based on the amount of data transferred out of AWS.

- **S3 Requester Pays**: A feature that allows the bucket owner to charge the requester for data transfer costs. This is useful for public heavy datasets where the owner wants to share the cost of data transfer with users. The owner pays for the storage costs, and the requester pays for the data transfer costs. The requester must be authenticated in AWS (cannot be anonymous).

## S3 Event Notifications:
   S3 can send event notifications to other AWS services whenever certain events occur in a bucket. This allows you to trigger workflows or processes based on S3 events. The supported events such as CreateObject, PutObject, DeleteObject, etc. The services that can receive notifications include:
   - **S3 Event Notifications**: 
      - **Amazon Simple Notification Service (SNS)**: Sends notifications to subscribers via email, SMS, or other protocols. SNS requires proper IAM permissions(SNS Resource Access Policy) to access S3 bucket.
      - **Amazon Simple Queue Service (SQS)**: Adds messages to a queue for processing by other applications or services. SQS requires proper IAM permissions(SQS Resource Access Policy) to access S3 bucket.
      - **AWS Lambda**: Triggers a Lambda function to process the event. This is useful for serverless architectures and real-time processing of S3 events. Lambda requires proper IAM permissions(Lambda Resource Access Policy) to access S3 bucket.
   - **Amazon EventBridge**: Whenever an event occurs in S3, **all event** is sent to EventBridge, Then you can notification to over 18 AWS services(Step function, Kinesis Streams, Firehose..) as destination through AWS EventBridge. It has advanced filtering capabilities and can be used to route events to different targets based on specific criteria(metadata,object size,name,.). It is useful for building event-driven architectures and integrating with other AWS services.

## S3 Baseline Performance:
   S3 automatically scales to high request rates, latency 100-200ms. Application can achieve **3,500 PUT/POST/DELETE requests per second** and **5,500 GET requests per second** in **per prefix in bucket**. To achieve higher request rates, you can use multiple prefixes in the bucket. 
   - If you have 2 prefixes, your bucket can achieve **7,000 PUT/POST/DELETE requests per second** and **11,000 GET requests per second**.
- **S3 Multipart Upload**: A feature that allows you to upload large objects in parts, this makes the file upload faster by parallel uploads. It is **recommended for file > 100 MB** and **must use for file > 5 GB**. In traditional upload, if the upload fails, you need to restart the entire upload. But with multipart upload, you can resume the upload from the last uploaded part, which saves time and bandwidth.
- **S3 Transfer Acceleration(Write)**: A feature that speeds up the upload and download of files from S3 bucket to clients by using Amazon CloudFront's globally distributed edge locations. It is useful for transferring large files over long distances. If uploading a file from India to Bucket on US region, it will be uploaded to the nearest edge location in India by public network, and then transferred to the S3 bucket in US region over AWS backbone network(private network).
- **S3 Byte Range Fetches(Read)**: A feature that allows you to fetch a file in parts by specifying the byte range. This is useful for large files, as it allows you to download only the portion of the file that you need, which can save time and bandwidth. These bytes are downloaded in parallel, which can further speed up the download process.
- **S3 Batch Operations**: A feature that allows you to perform bulk operations on existing S3 bucket using single Request. It is useful for modifying large numbers of objects, such as copying, tagging, or deleting, Encrypting unencrypted objects,etc,. A job consist of list of actions to be performed. We can use Athena to filter the objects and process it using S3 batch operations.

## S3 Storage Lens:
- A feature that provides visibility into your S3 storage usage and activity. It helps you understand, analyze and optimize entire AWS organization S3 usage. It discovers anomalies, identifies trends, and provides recommendations for cost optimization.
- Can aggregate data at the account, region, bucket, or prefix level.
- It creates a dashboard with metrics and insights about your S3 usage, such as storage class distribution, object size distribution, and access patterns. Can be configure to send daily metrics in .csv format to S3 bucket. 
- Provides a default dashboard with pre-configured metrics and insights which cannot be modified or deleted, but you can create custom dashboards with specific metrics and insights based on your requirements.
- Metrics provided for dashboard are:
   - **Storage Metrics**: Total storage used, number of objects, and storage class distribution.(identify fast growing/not used objects)
   - **Cost Optimization Metrics**: Estimated cost based on storage usage and request activity.(identify incomplete multipart uploads and non current versions of objects which can be moved to cheaper storage class or deleted)
   - **Data Protection Metrics**: Percentage of objects with versioning enabled, replication status, and encryption status.(identify unencrypted objects, object not following data protection policies)
   - **Access Management Metrics**: Provide insights for S3 bucket Ownership, public access, and access control policies.(identify public access buckets, bucket policies, ACLs and Ownership details)
   - **S3 Event Metrics**: Provides insights into S3 event notifications, including the number of events sent to different AWS services.(identify which events are being triggered and how frequently)
   - **Performance Metrics**: Provides insights into S3 Transfer Acceleration, S3 Select, and S3 Inventory.(identify which bucket has transfer acceleration enabled)
   - **Activity Metrics**: Provides insights into S3 object-level operations, including PUT, GET, DELETE, and LIST, downloads,. requests.(identify which objects/request are being accessed frequently)
   - **Detailed Status Code Metrics**: Provides insights into S3 request status codes, including success and error codes.(identify which requests are failing and why)

   - **Free Metrics**: Automatically available for all customers, contains around 28 metrics. Data is available for queries for 14 days
   - **Advanced Metrics**: 
      - Metrics: 
         - Activity Metrics
         - Advanced cost optimization metrics
         - Advanced data protection metrics
         - Detailed Status Code Metrics
      - Data is available for query for 15 months.
      - Collect metrics in Cloudwatch without any additional cost & can collect metrics at prefix level.

## S3 Security:
### S3 Encryption:
- Bucket policies are evaluated before encryption.
- **Server-Side Encryption (SSE)**: S3 automatically encrypts data at rest using server-side encryption. It supports three types of encryption:
   - **SSE-S3(S3 Managed keys)**
      - S3-managed keys (default). S3 handles the encryption and decryption process.
      - Objects are encrypted using AES-256 encryption algorithm. So must provide the header `"x-amz-server-side-encryption":"AES256"` while uploading the object.
      - This is enabled by default for all new objects in S3.
   - **SSE-KMS**
      - AWS Key Management Service (KMS) managed keys. Provides additional security and control over encryption keys. You can create, manage, and rotate your own keys.
      - KMS advantages: user control + audit key on CloudTrail.
      - Headers should be `"x-amz-server-side-encryption":"aws:kms"` while uploading the object.
      - The limitation of KMS is that the KMS API is called for download(decryption) and upload(encryption) of the object. So, the KMS API call is charged for each request. The cost of KMS is based on the number of requests and the amount of data processed. There is a limitaion of KMS quota per second & can be increased by requesting Service Quotas Console.
   - **SSE-DKMS**
      - AWS Key Management Service (KMS) managed keys with dual layer encryption. It is similar to SSE-KMS but provides an additional layer of security by encrypting the data with a customer-managed key before sending it to S3.
   - **SSE-C(Customer provided keys)**
      - You manage the encryption keys and provide them to S3 for encryption and decryption. S3 does not store the keys. You must provide the key for each request(upload/download).
      - HTTPS must be used to provide the encryption key to S3. The header should be `"x-amz-server-side-encryption-customer-algorithm":"AES256"` while uploading the object.
      - This encryption is only done at the CLI level(not supported in S3 console)
- **Client-Side Encryption**
   - You encrypt data before uploading it to S3. This provides additional security but requires you to manage the encryption cycle and keys. You can use AWS SDKs or third-party libraries to perform client-side encryption.
- **Encryption in Transit(SSL/TLS)**
   - Encryption in flight is done using SSL/TLS. 
   - S3 exposes two endpoints: HTTP (non-encrypted) and HTTPS (encrypted). It is recommended to use HTTPS for secure data transfer.
   - For SSE-C, HTTPS is mandatory to provide the encryption key to S3.
   - We can use **S3 Bucket Policy** to enforce the use of SSL/TLS for all requests to the bucket. This ensures that all data transferred to and from S3 is encrypted in transit. Example:
   ```json
   {
   "Version": "2012-10-17",
   "Statement": [
      {
         "Effect": "Deny",
         "Principal": "*",
         "Action": "s3:*",
         "Resource": "arn:aws:s3:::example-bucket/*",
         "Condition": {
         "Bool": {
            "aws:SecureTransport": "false"
         }
         }
      }
   ]
   }
   ```

### S3 CORS:
- Cross-Origin Resource Sharing (CORS) is a mechanism that allows web applications running in one domain to access resources in another domain/region. S3 supports CORS, allowing you to configure CORS rules for your buckets. If your application is hosted on a bucket and needs to access resources from another bucket, you should configure CORS to allow cross-origin requests.
- Should define allowed origins, methods, headers, and max age for preflight requests in the CORS configuration on the target bucket which is beign accessed(should define the source bucket in allowed origin which is accessing the target bucket).

### MFA on Delete:
- Multi-Factor Authentication (MFA) Delete is a feature that adds an additional layer of security to S3 buckets with **versioning enabled**. 
- This helps prevent accidental or malicious deletion of objects. **Only root user/bucket owner can enable MFA delete only using AWS CLI**.
- MFA will be required for:
   - Permanently deleting an object version.
   - Suspending versioning on the bucket.
- MFA won't be required for:
   - Enabling versioning on the bucket.
   - Deleting an object (creates a delete marker, but the object can be recovered by removing the delete marker).

### S3 Access Logs:
- Records detailed information about requests made to an S3 bucket (requester, action, response status, etc.). **Disabled by default**.
- Logs are stored in a **separate target bucket** (same region). **Never set the source bucket as the logging bucket** — causes an infinite logging loop.
- Log delivery is not real-time (may take a few hours). Can be analyzed using Amazon Athena.

### S3 Presigned URLs:
- A time-limited URL that grants temporary access to a private S3 object without AWS credentials. Generated by a user/role that already has permission to the object.
- The URL inherits the permissions of the generator — if that role loses access, the URL becomes invalid.
- **Expiration**:

   | Method      | Default | Maximum          |
   |-------------|---------|------------------|
   | S3 Console  | 1 hour  | 12 hours         |
   | AWS CLI/SDK | 1 hour  | 7 days (604800s) |

- Use cases: temporary file download, direct upload without exposing credentials.

### S3 Glacier Vault Lock:
- Enforces a **WORM (Write Once Read Many)** policy on a Glacier vault. Once locked, the policy **cannot be changed or deleted** — even by the root account.
- **Steps**: Initiate policy → validate within **24 hours** → complete lock (irreversible).

### S3 Object Lock:
- Provides **WORM** protection for S3 objects. **Versioning must be enabled**. Applied per object version.
- **Retention Modes**:
   - **Compliance Mode**: Nobody (including root) can delete/overwrite until retention expires. Period cannot be shortened.
   - **Governance Mode**: Most users cannot delete/overwrite, but users with `s3:BypassGovernanceRetention` permission can override.
- **Legal Hold**: Blocks deletion with no expiry date. Can be set/removed with `s3:PutObjectLegalHold` permission, independent of retention.

### S3 Access Points:
- Named network endpoints attached to a bucket, each with its own access policy. Simplifies managing access when multiple teams/apps share one bucket.
- Can be **VPC-restricted** — accepts requests only from within a specific VPC.
- **S3 Object Lambda Access Point**: Runs a Lambda function on-the-fly to transform object data before returning it to the caller (e.g., redacting PII). No need to store multiple versions of the same object.
