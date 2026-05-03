## VPC Endpoints

A **VPC Endpoint** lets your VPC privately connect to AWS services without going through the internet, IGW, NAT Gateway, or VPN — traffic stays entirely within AWS's internal network.

- No need to assign public IPs to instances just to call AWS services (S3, DynamoDB, SNS, etc.)
- Lower latency, no data transfer costs to the internet, more secure.
- Two types: 
    - **Interface Endpoint** 
    - **Gateway Endpoint**

---

## Interface Endpoint (AWS PrivateLink)

An **Interface Endpoint** creates an **ENI (Elastic Network Interface)** with a private IP inside your subnet — AWS service traffic is routed to this ENI instead of going to the public internet.

- Powered by **AWS PrivateLink**.
- Supports **most AWS services** — SSM, Secrets Manager, SNS, SQS, Kinesis, EC2 API, CloudWatch, etc.
- Billed **per hour + per GB** of data processed.
- Uses **Security Groups** to control which resources can use the endpoint.
- DNS resolves the AWS service's public hostname to the **private ENI IP** automatically (private DNS enabled).

```
Without Interface Endpoint:
EC2 (private subnet) → NAT Gateway → Internet → s3.amazonaws.com (public IP)

With Interface Endpoint:
EC2 (private subnet) → ENI (private IP in subnet) → AWS PrivateLink → S3
                        (no internet, no NAT needed)
```

---

## Gateway Endpoint

A **Gateway Endpoint** adds a route in your route table — traffic to S3 or DynamoDB is redirected to the AWS-managed endpoint prefix list, bypassing the internet.

- Supports **only S3 and DynamoDB**.
- **Free** — no hourly or data processing charge.
- Not an ENI — it's a route table entry. No Security Group, no private IP.
- Must update the **route table** of every subnet that should use it.
- Does not use PrivateLink.

```
Without Gateway Endpoint:
EC2 (private subnet) → NAT Gateway → Internet → s3.amazonaws.com

With Gateway Endpoint (route table updated):
EC2 (private subnet) → Gateway Endpoint (pl-xxxxxxxx) → S3
                        (no internet, no NAT needed)
```

**Route table after adding Gateway Endpoint:**
```
Destination          Target
10.0.0.0/16       →  local
pl-xxxxxxxx (S3)  →  vpce-xxxxxxxx    ← AWS-managed prefix list for S3
0.0.0.0/0         →  nat-xxxxxxxx
```

---

## Interface vs Gateway Endpoint

| | Interface Endpoint | Gateway Endpoint |
|---|---|---|
| **Supported services** | Most AWS services | S3 and DynamoDB only |
| **How it works** | ENI with private IP in subnet | Route table entry |
| **Cost** | Per hour + per GB | Free |
| **Security Groups** | Yes | No |
| **DNS** | Private DNS resolves to ENI IP | No DNS change — route-based |
| **Powered by** | AWS PrivateLink | AWS-managed prefix list |
| **Subnet-specific** | Yes — deployed in chosen subnet | No — route table association |

---

## Endpoint Policy

Both endpoint types support an **Endpoint Policy** — a resource-based policy that controls which AWS resources/actions can be accessed through the endpoint.

**Example:** Allow access to a specific S3 bucket only:
```json
{
  "Statement": [{
    "Effect": "Allow",
    "Principal": "*",
    "Action": "s3:*",
    "Resource": [
      "arn:aws:s3:::my-bucket",
      "arn:aws:s3:::my-bucket/*"
    ]
  }]
}
```

This prevents instances from using the endpoint to access any S3 bucket other than `my-bucket` — useful for data exfiltration prevention.
