## AWS VPN(Virtual Private Network)

AWS VPN is a service that allows you to create secure connections between your on-premises networks and AWS cloud resources. It enables hybrid cloud architectures by establishing encrypted tunnels over the public internet or AWS Direct Connect.

There are two main types of VPNs in AWS:
1. **Site-to-Site VPN**: Connects your on-premises network to an AWS VPC using **IPsec VPN tunnels**. This is typically used for connecting data centers or branch offices to AWS.
2. **Client VPN**: Allows users to securely access AWS resources from any location using an OpenVPN-based client (SSL). This is particularly useful for remote work scenarios and provides a secure connection to AWS services.

**Interview Questions:**
1. **Difference between AWS VPN and Direct Connect**: AWS VPN is a secure connection over the internet, while Direct Connect provides a dedicated, private connection to AWS. Direct Connect offers lower latency and higher bandwidth compared to VPN.