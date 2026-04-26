## Amazon QuickSight

Amazon QuickSight is a serverless, cloud-native **Business Intelligence (BI)** service for creating interactive dashboards, visualizations, and data stories — no infrastructure to manage.

**Key Concepts:**
- **SPICE (Super-fast, Parallel, In-memory Calculation Engine)**: In-memory data store that caches imported data for fast, interactive query performance — reduces load on source databases.
- **Dataset**: A connection to a data source with optional transformations (filters, calculated fields, joins).
- **Analysis**: A workspace for building visuals and charts from datasets.
- **Dashboard**: A published, read-only view of an analysis — shared with users or groups.

**Data Sources:**
- AWS: S3, Athena, Redshift, RDS, Aurora, OpenSearch, Timestream.
- SaaS: Salesforce, ServiceNow, Jira, GitHub.
- On-premises: via JDBC connectors (MySQL, PostgreSQL, SQL Server).

**Users & Pricing:**
- **Authors**: Create and publish dashboards — monthly per-user pricing.
- **Readers**: View dashboards only — **pay-per-session** pricing (max $5/month/user) — cost-effective for large organizations.
- **Groups**: Collections of users — assign dataset permissions, RLS rules, and dashboard access at the group level instead of per user. Groups are managed within QuickSight (not IAM groups).

**Editions:**

| Feature | Standard | Enterprise |
|---------|----------|------------|
| Data sources | All standard sources | All + private VPC sources |
| SPICE | Yes | Yes |
| Users | IAM or QuickSight users | IAM, QuickSight groups, Active Directory (AD) |
| Row-Level Security | Yes | Yes |
| Column-Level Security | No | Yes |
| ML Insights | Limited | Full (anomaly, forecasting, Q) |
| Encryption at rest | No | Yes (KMS) |
| Reader sessions | Yes | Yes |
| AD integration | No | Yes (Microsoft Active Directory) |

- **Standard**: Suitable for small teams with basic BI needs.
- **Enterprise**: For organizations needing AD integration, column-level security, encryption at rest, and advanced ML features.

**Security:**
- **Row-Level Security (RLS)**: Restrict which rows a user can see in a dataset — based on user/group rules.
- **Column-Level Security**: Restrict specific columns from certain users.
- Deployed in a VPC or public — can connect to private data sources via VPC connection.

**ML Insights:**
- **Anomaly Detection**: Automatically detect outliers in your data using ML.
- **Forecasting**: Predict future trends based on historical data.
- **Natural Language Narratives**: Auto-generate plain-English summaries of charts.
- **Q (QuickSight Q)**: Ask questions about your data in natural language — QuickSight generates the visual answer.

**Use cases:** KPI dashboards, business reporting, ad-hoc exploration, embedded analytics in applications.
