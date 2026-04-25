## Amazon CloudWatch

Amazon CloudWatch is a monitoring and observability service that collects metrics, logs, and events from AWS resources and applications.

**Key Concepts:**
- **Metrics**: Time-series data points from AWS services (CPU, memory, request count, etc.). Default metrics are free; custom metrics have a cost.
- **Alarms**: Trigger actions (SNS notification, Auto Scaling, EC2 action) when a metric crosses a threshold.
- **Logs**: Collect, monitor, and store log files from EC2, Lambda, CloudTrail, and more via **CloudWatch Logs**.
- **Log Groups / Log Streams**: Logs are organized into log groups (per service/app) and log streams (per instance/execution).
- **Dashboards**: Customizable visual panels for metrics across regions.
- **Events / EventBridge**: React to state changes in AWS resources (e.g., EC2 state change → trigger Lambda).
- **CloudWatch Agent**: Installed on EC2 to collect system-level metrics (memory, disk) and custom logs not available by default.

**Metric Retention:**
- High-resolution metrics (< 60s): retained 3 hours.
- Standard metrics (60s): retained 15 days.
- Aggregated metrics: retained up to 15 months.
