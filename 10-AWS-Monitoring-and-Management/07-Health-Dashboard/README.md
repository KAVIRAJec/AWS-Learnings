## AWS Health Dashboard

AWS provides two health dashboards to monitor the status of AWS services.

### Service Health Dashboard (now: AWS Health — Service History):
- Public page showing the **current and historical status** of all AWS services across all regions.
- Shows ongoing incidents and outages affecting AWS globally.
- Not account-specific — shows status for all customers.

### Personal Health Dashboard (now: AWS Health — Your Account):
- **Account-specific** alerts when AWS events may impact your resources.
- Shows proactive notifications (e.g., EC2 host maintenance, certificate expiry).
- Provides **remediation guidance** for affected resources.
- Can be integrated with **EventBridge** to trigger automated responses (e.g., notify via SNS, run Lambda).
