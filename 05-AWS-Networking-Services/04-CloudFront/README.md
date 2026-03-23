## AWS CloudFront

Amazon CloudFront is a Content Delivery Network (CDN) service that securely delivers data, videos, applications, and APIs to users globally with low latency and high transfer speeds. It uses a network of edge locations to cache content closer to users, improving performance and reducing latency.

**Key Features:**
1. **Edge Locations**: CloudFront has a global network of edge locations that cache content closer to users, reducing latency and improving performance.
2. **Dynamic Content**: CloudFront can deliver dynamic content, allowing for personalized experiences for users.
3. **Integration with AWS Services**: CloudFront integrates seamlessly with other AWS services like S3, EC2, and Lambda, making it easy to deliver content from these services.
4. **Security**: CloudFront provides built-in security features like SSL/TLS encryption, AWS Shield for DDoS protection, and AWS WAF for web application firewall capabilities.
5. **Expiration**: CloudFront allows you to set expiration times for cached content, ensuring that users receive the most up-to-date information.

**WorkFlow:**
1. **Request**: A user requests content from a CloudFront distribution.
2. **Edge Location**: The request is routed to the nearest edge location based on latency.
3. **Cache Check**: CloudFront checks if the content is already cached at the edge location.
4. **Cache Hit**: If the content is cached, it is delivered to the user from the edge location.
5. **Cache Miss**: If the content is not cached, CloudFront retrieves it from the origin server (e.g., S3 bucket, EC2 instance) and caches it at the edge location for future requests. Next time, the content will be delivered from the edge location, reducing latency.
