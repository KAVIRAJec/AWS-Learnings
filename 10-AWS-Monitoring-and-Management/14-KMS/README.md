## AWS KMS (Key Management Service)

AWS KMS is a managed service to **create, control, and rotate encryption keys** used to protect data across AWS services.

**Key Concepts:**
- **CMK (Customer Master Key)**: The primary key resource in KMS. Used to encrypt/decrypt data or generate data keys.
   - **AWS Managed Keys**: Created and managed by AWS for specific services (e.g., `aws/s3`). No cost, no control.
   - **Customer Managed Keys**: Created by you — full control over rotation, policies, and deletion. Charged per key/month.
- **Data Key**: KMS generates a data key using a CMK — used to encrypt large data (envelope encryption). KMS does not store the data key.
- **Envelope Encryption**: Encrypt data with a data key → encrypt the data key with a CMK → store both. KMS only handles the key encryption, not the data itself.
- **Key Rotation**: Automatic annual rotation available for customer-managed keys.
- **Key Policy**: Resource-based policy on the CMK that controls who can use or manage it.
- Integrated with S3, EBS, RDS, Lambda, CloudTrail, and more.
