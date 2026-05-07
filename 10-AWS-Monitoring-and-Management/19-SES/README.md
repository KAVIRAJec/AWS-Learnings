## AWS SES (Simple Email Service)

**AWS SES** is a managed email sending service — used to send transactional emails, marketing emails, and notifications at scale directly from your application.

- Send emails via **SMTP** or the **SES API/SDK**.
- Handles deliverability, bounce handling, spam complaints, and reputation management.
- Supports sending and **receiving** emails.
- Pay per email sent — no minimum fee.

---

## Key Concepts

- **Verified Identity**: You must verify the sender's email address or domain before sending — proves you own it.
- **Sandbox Mode**: New accounts start in sandbox — can only send to verified addresses (prevents spam). Request production access to send to anyone.
- **Sending Limits**: Daily sending quota + max send rate (emails/second) — increases as reputation builds.
- **Configuration Set**: A group of rules applied to emails — enables event tracking (bounces, complaints, deliveries) via SNS, CloudWatch, or Kinesis Firehose.
- **Suppression List**: SES automatically suppresses addresses that bounced or complained — protects your sender reputation.

---

## Use Cases

- **Transactional emails** — order confirmations, password resets, OTP codes.
- **Bulk/marketing emails** — newsletters, promotional campaigns.
- **Application alerts** — send email notifications from Lambda, EC2, or any app using the SDK.
- **Receiving emails** — receive inbound emails and trigger Lambda or store in S3.

---

## SES vs SNS (for email)

| | SES | SNS (email subscription) |
|---|---|---|
| **Purpose** | Rich email sending (HTML, attachments) | Simple text notification via email |
| **Control** | Full — custom from/reply-to, templates, tracking | Minimal — plain text only |
| **Bulk sending** | Yes | No |
| **Receiving email** | Yes | No |
| **Best for** | App-generated emails, marketing | Operational alerts, CloudWatch alarms |
