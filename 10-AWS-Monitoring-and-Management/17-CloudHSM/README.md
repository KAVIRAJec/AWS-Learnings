## AWS CloudHSM

AWS CloudHSM provides **dedicated, single-tenant Hardware Security Modules (HSMs)** in the AWS cloud — you get physical hardware exclusively for your use to generate, store, and manage cryptographic keys with the highest level of isolation and compliance.

- Unlike KMS (multi-tenant, AWS-managed HSM infrastructure), CloudHSM gives you **full exclusive control** over the hardware.
- AWS manages the physical hardware, networking, and patching — but has **zero access** to your keys or cryptographic operations.
- Deployed inside your **VPC** — accessible only from within your network, not over the public internet.

---

## Key Characteristics

- **FIPS 140-2 Level 3 certified** — the highest HSM certification level. Required by certain government, financial, and healthcare compliance frameworks. KMS is only FIPS 140-2 Level 2.
- **Single-tenant** — dedicated physical HSM device per customer. No sharing with other AWS accounts.
- **You manage keys entirely** — AWS cannot recover keys if lost. No backup by AWS. You are fully responsible for key backup and durability.
- **No AWS integration by default** — unlike KMS which natively integrates with S3, EBS, RDS, etc., CloudHSM requires custom application-level integration using standard cryptographic APIs.

---

## CloudHSM Cluster

- CloudHSM resources are grouped into a **cluster** — a collection of HSMs that synchronize keys and policies automatically.
- **High availability**: Deploy HSMs across **multiple AZs** within a cluster — if one HSM fails, others continue serving requests seamlessly.
- **Automatic synchronization**: Keys created on one HSM in the cluster are replicated to all others within seconds.
- Minimum recommended: **2 HSMs in 2 AZs** for production workloads.

---

## Supported Cryptographic Operations

- **Symmetric encryption**: AES (128, 192, 256-bit)
- **Asymmetric encryption**: RSA, ECC
- **Hashing**: SHA-1, SHA-256, SHA-384, SHA-512
- **Digital signing**: RSA, ECDSA
- **SSL/TLS offloading**: Offload private key operations from web servers to CloudHSM — improves server performance and keeps private keys off the server entirely
- **Oracle TDE (Transparent Data Encryption)**: Store Oracle DB master keys in CloudHSM

---

## Access & Integration

- Accessed via **industry-standard APIs**: PKCS#11, Java JCE (Java Cryptography Extension), Microsoft CNG/CAPI — works with existing applications without major code changes.
- **No native AWS service integration** (unlike KMS) — you write application code to call CloudHSM directly via these APIs.
- Can be used as a **Custom Key Store for KMS** — KMS operations (Encrypt, GenerateDataKey, etc.) are proxied to your CloudHSM cluster. This gives you KMS-style AWS service integration with CloudHSM-level key isolation. See [KMS README](../14-KMS/README.md).
- Accessible only from within the **VPC** where it's deployed — or via VPC peering.

---

## KMS vs CloudHSM

| | AWS KMS | AWS CloudHSM |
|---|---|---|
| **Tenancy** | Multi-tenant (shared HSM infrastructure) | Single-tenant (dedicated hardware) |
| **Key control** | AWS manages HSM, you manage keys | You manage everything — AWS has zero access |
| **FIPS compliance** | Level 2 | Level 3 |
| **AWS integration** | Native — S3, EBS, RDS, Lambda, etc. | None by default — custom code via PKCS#11 / JCE |
| **HA setup** | Managed by AWS automatically | You deploy HSMs across AZs in a cluster |
| **Cost** | Per API call + per key/month | Per HSM instance/hour (~$1.60/hr) |
| **Key recovery** | AWS can assist with recovery | No recovery if keys lost — your responsibility |
| **Best for** | Most workloads, ease of use, AWS integration | Strict compliance (FIPS L3), full key ownership, SSL offloading |
