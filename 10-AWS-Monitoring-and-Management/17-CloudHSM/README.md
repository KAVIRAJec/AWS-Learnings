## AWS CloudHSM

AWS CloudHSM provides dedicated **hardware security modules (HSMs)** in the AWS cloud for generating and managing cryptographic keys with the highest level of security.

**Key Concepts:**
- Unlike KMS (multi-tenant), CloudHSM is **single-tenant** — you get dedicated hardware.
- You manage your own keys entirely — AWS has no access to keys or operations.
- Compliant with **FIPS 140-2 Level 3** — required for some regulatory frameworks.
- Deployed in a **VPC** — only accessible from within your network.
- Supports symmetric and asymmetric keys, SSL/TLS offloading, and digital signing.

**KMS vs CloudHSM:**
- KMS: AWS manages HSM infrastructure, shared, easier integration, lower cost.
- CloudHSM: You control everything, dedicated hardware, stricter compliance, higher cost.
