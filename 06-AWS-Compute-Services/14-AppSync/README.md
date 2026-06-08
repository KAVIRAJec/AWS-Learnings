## AWS AppSync

AWS AppSync is a **fully managed GraphQL API service** — it lets you build flexible, real-time APIs that aggregate data from multiple sources in a single query.

---

## GraphQL vs REST

| | REST API | GraphQL (AppSync) |
|---|---|---|
| **Endpoint** | Multiple endpoints (`/users`, `/orders`, `/products`) | Single endpoint (`/graphql`) |
| **Data fetching** | Fixed response shape per endpoint | Client specifies exactly which fields it needs |
| **Over/under-fetching** | Common — GET `/user` returns everything | Eliminated — only requested fields returned |
| **Real-time** | Requires polling or WebSocket workaround | Native subscriptions via WebSocket |
| **Multiple sources** | Client makes multiple requests | One query can merge data from multiple backends |

---

## How AppSync Works

```
Client (web / mobile)
        │
        │  GraphQL query / mutation / subscription
        ▼
AWS AppSync (GraphQL engine)
        │
        ├── Resolver 1 ──► DynamoDB table
        ├── Resolver 2 ──► Lambda function
        ├── Resolver 3 ──► RDS (Aurora Serverless)
        ├── Resolver 4 ──► OpenSearch
        └── Resolver 5 ──► HTTP endpoint (any REST API)
        │
        ▼
Single merged response returned to client
```

- **Schema**: Defines the data types, queries, mutations, and subscriptions in GraphQL SDL.
- **Resolver**: Connects each field in the schema to a data source — translates GraphQL requests into the data source's native language (DynamoDB expression, Lambda invocation, SQL, etc.).
- **Data source**: The backend AppSync fetches data from.

---

## Data Sources

| Data Source | Use |
|---|---|
| **Amazon DynamoDB** | Most common — CRUD operations on NoSQL tables |
| **AWS Lambda** | Custom logic, any backend — Lambda handles the data fetch |
| **Amazon RDS (Aurora Serverless)** | Relational data via Data API |
| **Amazon OpenSearch** | Full-text search, analytics queries |
| **HTTP endpoint** | Any external REST API or microservice |
| **None** | Local resolver — for mocking or static data |

---

## Real-Time Subscriptions

- Clients subscribe to data changes using **GraphQL subscriptions**.
- AppSync pushes updates to subscribed clients over **WebSocket (MQTT over WebSocket)** — no polling needed.
- A subscription is triggered by a **mutation** — when data is written/updated, all subscribed clients receive the change in real time.

```
Client A  ──subscribe──►  AppSync
Client B  ──mutation──►   AppSync  ──► DynamoDB updated
                              │
                              └──► pushes update to Client A via WebSocket
```

**Use cases:** Live dashboards, chat applications, collaborative editing, real-time notifications, stock tickers.

---

## Offline Support & Data Sync

- AppSync supports **offline data access and synchronization** for mobile apps via the **Amplify DataStore**.
- Clients can read and write data locally while offline — changes are queued and synced to AppSync when connectivity is restored.
- Conflict detection and resolution is handled automatically (last-write-wins or custom Lambda resolver).

---

## Authentication

AppSync supports multiple auth modes on the same API — different operations can use different auth:

| Auth Mode | How |
|---|---|
| **API Key** | Simple key in request header — for public or dev/test APIs |
| **Amazon Cognito User Pools** | JWT token from Cognito — for authenticated users |
| **IAM** | AWS Signature V4 — for server-to-server or AWS service access |
| **OIDC** | Any OpenID Connect provider (Auth0, Okta, etc.) |
| **Lambda** | Custom Lambda function validates the token — full flexibility |

---

## Caching

- AppSync has a built-in **server-side cache** — responses are cached at the AppSync layer, reducing calls to data sources.
- Configurable TTL per resolver.
- Reduces latency and data source load for frequently queried data.

---

## AppSync vs API Gateway

| | AWS AppSync | Amazon API Gateway |
|---|---|---|
| **API type** | GraphQL only | REST, HTTP, WebSocket |
| **Real-time** | Native subscriptions (WebSocket) | WebSocket API (manual setup) |
| **Data aggregation** | Yes — single query across multiple sources | No — one integration per endpoint |
| **Client field selection** | Yes — client specifies fields | No — fixed response shape |
| **Offline sync** | Yes (via Amplify DataStore) | No |
| **Best for** | Mobile/web apps needing flexible queries, real-time, multi-source | REST APIs, microservices, backend routing |
