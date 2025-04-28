## AWS Transit Gateway

AWS Transit Gateway is a network transit hub that connects multiple VPCs, on-premises networks, and AWS services through a central hub (centralized router). Instead of building many-to-many VPC peerings, Transit Gateway enables a hub-and-spoke model.
![alt text](image.png)
### Key Features
- **Centralized Management**: Simplifies network management by providing a single point of control for routing traffic between multiple VPCs and on-premises networks.
- **Scalability**: Can scale to support thousands of VPCs and on-premises networks.
- **Routing Control**: Provides advanced routing capabilities, including route propagation and route filtering, to control how traffic flows between connected networks.
- **Multi-Region Support**: Can connect VPCs and on-premises networks across multiple AWS Regions, enabling global network architectures.
- **Private and Public Connectivity**: Supports both private and public connectivity, allowing you to connect to AWS services like Amazon S3 and AWS Lambda.

### Interview Questions

#### Difference between VPC Peering and Transit Gateway

| Feature         | Transit Gateway                          | VPC Peering                          |
|------------------|------------------------------------------|---------------------------------------|
| **Scalability**  | Highly scalable (thousands of VPCs)      | Limited to 125 peering connections per VPC |
| **Routing**      | Centralized (route tables at TGW level)  | Decentralized (per VPC pair)          |
| **Management**   | Simplified in large environments         | Becomes complex at scale              |
| **Cross-Region** | Supported with peering                  | Supported with inter-region VPC peering |

2. **Difference between Transit Gateway and Direct Connect**
   - **Transit Gateway**: A service that connects VPCs and on-premises networks through a central hub.
   - **Direct Connect**: A dedicated network connection from on-premises to AWS, bypassing the internet for lower latency and more consistent performance.