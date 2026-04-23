## AWS Transfer Family

AWS Transfer Family is a fully managed service that enables file transfers into and out of AWS storage (**S3** or **EFS**) using standard transfer protocols — without modifying existing client-side workflows.

**Supported Protocols:**
- **SFTP** (SSH File Transfer Protocol) — encrypted file transfer over SSH.
- **FTPS** (FTP over SSL) — FTP with TLS encryption.
- **FTP** (File Transfer Protocol) — unencrypted, only supported within a VPC (not public internet).
- **AS2** (Applicability Statement 2) — used for B2B data exchange (e.g., EDI transactions).

**Key Concepts:**
- **Endpoint**: A Transfer Family server endpoint that clients connect to. Can be public or VPC-hosted (private).
- **Storage Backend**: Files transferred are stored directly in **S3** or **EFS** — no intermediate storage.
- **Identity Provider**: Supports **AWS managed** (Service-managed), **Microsoft AD**, or a **custom identity provider** (via API Gateway + Lambda) for authenticating users.
- **Users**: Each user can be mapped to a specific S3 prefix or EFS path, restricting access to their designated area.

**Use cases:**
- Migrate existing FTP/SFTP workflows to AWS without changing client tools.
- Partner file exchange (B2B) using AS2.
- Securely receive files from external vendors directly into S3/EFS.

**Availability:**
- Multi-AZ by default when deployed with a **VPC endpoint** — AWS manages redundancy across AZs.
- Scalable, reliable infrastructure with built-in failover.

**Cost:**
- Billed per **protocol endpoint enabled per hour** (e.g., SFTP server running = charged hourly even with no transfers).
- Additional charge per **GB of data uploaded and downloaded**.
- No charge for the underlying S3/EFS storage itself (billed separately by those services).
