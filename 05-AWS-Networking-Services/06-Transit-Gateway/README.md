## AWS Transit Gateway (centralized router)

AWS Transit Gateway is a network transit hub that connects multiple VPCs, on-premises networks, and AWS services through a central hub (centralized router). Instead of building many-to-many VPC peerings, Transit Gateway enables a hub-and-spoke model.
![alt text](image.png)
### Key Features
- **Centralized Management**: Simplifies network management by providing a single point of control for routing traffic between multiple VPCs and on-premises networks.
- **Scalability**: Can scale to support thousands of VPCs and on-premises networks.
- **Routing Control**: Provides advanced routing capabilities, including route propagation and route filtering, to control how traffic flows between connected networks.
- **Multi-Region Support**: Can connect VPCs and on-premises networks across multiple AWS Regions, enabling global network architectures.
- **Private and Public Connectivity**: Supports both private and public connectivity, allowing you to connect to AWS services like Amazon S3 and AWS Lambda.
