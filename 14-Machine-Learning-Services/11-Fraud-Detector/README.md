## Amazon Fraud Detector

Amazon Fraud Detector is a fully managed service for **building and deploying ML-based fraud detection models** — trained on your own historical data and Amazon's 20+ years of fraud detection experience.

**Key Concepts:**

- **Event**: A data point submitted for fraud evaluation — a payment transaction, account registration, login attempt, or order.
- **Entity**: The actor in an event — a customer, account, or device.
- **Variable**: An input attribute used for prediction — IP address, email domain, order amount, device fingerprint, account age, shipping address.
- **Model**: Trained on your labeled historical event data (fraud vs. legitimate). Supported model types:

| Model Type | Best For |
|---|---|
| **Online Fraud Insights (OFI)** | New account fraud and payment fraud — evaluates isolated events |
| **Transaction Fraud Insights (TFI)** | Payment fraud using historical transaction patterns per entity |
| **Account Takeover Insights (ATI)** | Account compromise — analyzes login event sequences |

- **Detector**: Defines the complete evaluation logic — combines a model's score with explicit rules to produce an outcome.
- **Rules**: Condition-based logic on variables or model scores — expressed as simple expressions:
   - Example: `$fraud_score > 900 → BLOCK`
   - Example: `$email_domain == "temp-mail.org" → REVIEW`
- **Outcomes**: The decision returned by the Detector — fully custom (e.g., APPROVE, REVIEW, BLOCK).

**How it Works:**
1. Upload labeled historical event data to S3 (CSV with fraud/legitimate labels).
2. Train a model — Fraud Detector handles feature engineering and model training automatically.
3. Create a **Detector** combining the model score with your business rules.
4. Call `GetEventPrediction` API in real time — returns an outcome + model fraud score (0–1000).

**Real-Time Scoring:**
- `GetEventPrediction`: Synchronous API — returns outcome and model scores in milliseconds.
- `SendEvent`: Record events for model updates and labeling (mark events as fraud after the fact).

**Batch Predictions:**
- Submit a batch of events from S3 → Fraud Detector writes prediction results back to S3.

**Use cases:** Payment card fraud detection, new account fraud (fake registrations), account takeover (credential stuffing), promotional and coupon abuse, online marketplace fraud, insurance claim fraud.
