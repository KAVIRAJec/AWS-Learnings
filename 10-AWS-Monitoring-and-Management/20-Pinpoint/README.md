## AWS Pinpoint

**AWS Pinpoint** is a customer engagement service for sending **targeted, personalized messages** to users across multiple channels — email, SMS, push notifications, and voice. Built for marketing and user engagement, not just transactional sending.

- Segment your audience by attributes (location, device, behaviour, custom data).
- Run **campaigns** (scheduled bulk sends) or **journeys** (automated multi-step flows based on user actions).
- Built-in **analytics** — track open rates, clicks, conversions, and opt-outs.
- Uses **SES** under the hood for email sending.

---

## Channels

| Channel | Use case |
|---|---|
| **Email** | Newsletters, promotions, onboarding sequences |
| **SMS** | OTPs, alerts, promotional texts |
| **Push Notifications** | Mobile app re-engagement, offers |
| **Voice** | Automated voice messages / reminders |
| **In-app messaging** | Messages shown inside your mobile/web app |

---

## Key Concepts

- **Segment** — a group of users filtered by attributes (e.g. users in India who haven't opened the app in 7 days).
- **Campaign** — a one-time or recurring message sent to a segment at a scheduled time.
- **Journey** — a multi-step automated workflow: user signs up → send welcome email → wait 3 days → if no purchase, send discount SMS.
- **Event Stream** — stream engagement events (opens, clicks, installs) to Kinesis for custom analytics.

---

## SES vs Pinpoint

| | SES | Pinpoint |
|---|---|---|
| **Focus** | Transactional email sending | Multi-channel customer engagement & marketing |
| **Audience targeting** | No — you specify recipients | Yes — segments by attributes and behaviour |
| **Campaigns / Journeys** | No | Yes |
| **Channels** | Email only | Email, SMS, push, voice, in-app |
| **Analytics** | Bounces, complaints, delivery | Open rates, clicks, conversions, revenue |
| **Best for** | Password resets, OTPs, app-triggered emails | Marketing campaigns, user re-engagement |
