## Amazon Kendra

Amazon Kendra is a managed **intelligent enterprise search** service powered by ML — understands natural language questions and returns precise answers from your organization's content, not just a list of links.

**Key Concepts:**

- **Index**: The core resource — ingests, processes, and stores your documents for search. One index per search application.
- **Data Sources**: Connectors that crawl and sync content from repositories on a schedule:
   - AWS: **S3, RDS, Aurora**
   - SaaS: **SharePoint, Confluence, ServiceNow, Salesforce, OneDrive, GitHub, Jira, Zendesk, Box**
   - Custom: **Custom data source API** for any system
- **Document**: Any content added to the index — PDFs, Word docs, HTML pages, plain text, PowerPoint files, FAQs. Documents are chunked and indexed automatically.

**Query Types Kendra Handles:**

| Query Type | Description | Example |
|---|---|---|
| **Factoid** | Direct answer extracted from text | "What is the vacation policy?" |
| **Descriptive** | Returns the most relevant document passage | "How do I reset my password?" |
| **Keyword** | Traditional keyword matching — fallback | "password reset steps" |

**Relevance Tuning:**
- Boost or demote specific document attributes, data sources, or fields — tune ranking without retraining.
- Example: Boost documents tagged as `type=FAQ` over `type=blog`.

**Access Control:**
- Document-level ACL enforcement — users only see results for documents they have permission to view.
- Syncs ACLs from connected sources (SharePoint permissions, Confluence spaces, S3 bucket policies).
- Supports IAM, SAML, and OIDC-based user context filtering.

**Incremental Sync:**
- Data source connectors track which documents have changed — only re-indexes new or modified content. Reduces indexing cost and time.

**Editions:**

| Feature | Developer | Enterprise |
|---------|-----------|------------|
| Capacity | Limited | High |
| Connectors | Standard | Standard + more |
| Availability | Single AZ | Multi-AZ (HA) |
| Use case | Prototyping | Production |

**Kendra Intelligent Ranking:**
- A standalone API — re-rank results from your existing search system (OpenSearch, Elasticsearch) using Kendra's ML relevance model without building a full Kendra index.

**Use cases:** Internal employee knowledge base search, customer-facing help center and support portals, compliance and legal document discovery, IT self-service portals, research and content management search.
