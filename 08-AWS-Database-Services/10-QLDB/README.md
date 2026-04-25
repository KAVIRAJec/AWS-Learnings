## Amazon QLDB (Quantum Ledger Database)

Amazon QLDB is a fully managed **ledger database** with a transparent, immutable, and cryptographically verifiable transaction log.

**Key Concepts:**
- Every change to data is recorded in an **immutable journal** — data cannot be altered or deleted.
- Uses **cryptographic hashing (SHA-256)** to verify the integrity of the entire history of changes.
- Serverless — automatically scales storage and compute.
- Uses **PartiQL** (SQL-compatible) for querying.
- Unlike blockchain (decentralized), QLDB is **centralized** — owned and managed by you (no distributed consensus needed).

**Use cases:** Financial transaction ledgers, supply chain tracking, medical record history, any system requiring an auditable, tamper-proof history.
