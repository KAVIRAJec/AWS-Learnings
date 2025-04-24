## AWS CICD(Continuous Integration and Continuous Deployment)

CI/CD is the practice of automating the software development lifecycle. AWS provides a collection of services to help you build, test, and deploy applications faster and more reliably with full control over your delivery pipelines. These services streamline software delivery, improve code quality, and reduce deployment risks.

### AWS CodeCommit
AWS CodeCommit is AWS managed Secure and scalable source control service (Git-based).
#### Features:
- Supports Git-based workflows.
- Fully managed and integrated with other AWS services.
- No size limits on repositories.
- Supports pull requests, branching, and code reviews.

#### Use Case:
Hosting private Git repositories for application code.
- Storing and versioning configuration files.

#### Pricing:
- Free tier: 5 active users per month.
- $1 per active user per month after the free tier.

#### AWS CodeBuild
AWS CodeBuild is a fully managed build service that compiles source code, runs tests, and produces software packages ready for deployment.

#### Features:
- Fully managed and scalable.
- Supports multiple programming languages and build environments.
- Integrates with AWS CodeCommit, GitHub, and Bitbucket.
- Provides build logs and reports.
- Supports Docker images for custom build environments.

#### Use Case:
- Building and testing applications in a CI/CD pipeline.
- Running unit tests and integration tests.

#### Pricing:
- Pay-as-you-go pricing based on the number of build minutes used.
- Free tier: 100 build minutes per month for the first 12 months.
- Additional build minutes are charged at $0.008 per minute.

### AWS CodePipeline
AWS CodePipeline is a fully managed continuous integration and continuous delivery (CI/CD) service that automates the build, test, and release process for applications. 
- It automates the CodeBuild and deployment process when changes are made to the code repository(AWS CodeCommit, GitHub, etc.).

#### Features:
- Fully managed and scalable.
- Integrates with AWS services and third-party tools(e.g., GitHub, Jenkins).
- Supports multiple stages in the pipeline (e.g., build, test, deploy).
- Provides visual representation of the pipeline.
- Supports manual approval actions.
- Can trigger external CD pipelines.

#### Use Case:
- Automating the deployment of applications to multiple environments (e.g., dev, test, prod).
- Integrating with third-party tools for testing and deployment.

#### Pricing:
- Pay-as-you-go pricing based on the number of pipeline executions.
- Free tier: 1 active pipeline per month for the first 12 months.


### AWS CodeDeploy
AWS CodeDeploy is a fully managed deployment service that automates the deployment of applications to Amazon EC2 instances, on-premises servers, EKS containers, and Lambda functions.

#### Features:
- Fully managed and scalable.
- Supports blue/green and rolling deployment strategies.
- Integrates with AWS CodePipeline and other CI/CD tools.
- Provides deployment monitoring and rollback capabilities.
- Supports deployment to multiple environments (e.g., EC2, Lambda, ECS).
- Can deploy applications from S3, GitHub, or Bitbucket.
- Can deploy from docker images stored in Amazon ECR, Docker Hub, or any other registry.
- Can deploy to on-premises servers using AWS CodeDeploy agent.

#### Use Case:
- Automating the deployment of applications to Amazon EC2 instances, on-premises servers, EKS containers, and Lambda functions.
- Rolling back deployments in case of failures.

#### Pricing:
- Pay-as-you-go pricing based on the number of deployment instances and deployment types.
- Free tier: 10 deployment instances per month for the first 12 months.
- Additional deployment instances are charged at $0.02 per instance for EC2 and on-premises servers, and $0.01 per instance for Lambda and ECS.

### AWS CodeStar
AWS CodeStar is a fully managed service that provides a unified user interface for managing software development projects on AWS. It integrates with other AWS services like CodeCommit, CodeBuild, CodeDeploy, and CodePipeline to provide a complete CI/CD solution.

#### Features:
- Unified user interface for managing software development projects.
- Provides project templates for different programming languages and frameworks.
- Integrates with AWS services and third-party tools.
- Provides project management features like issue tracking and team collaboration.

#### Use Case:
- Creating and managing software development projects on AWS.
- Integrating with other AWS services for CI/CD.

#### Pricing:
- Free tier: 1 active project per month for the first 12 months.
- Additional active projects are charged at $0.10 per project per month.

### Other AWS Services for CI/CD
- **AWS CodeArtifact**: A fully managed artifact repository service that makes it easy to store, publish, and share software packages used in your CI/CD pipelines.
- **AWS Cloud9**: A cloud-based integrated development environment (IDE) that allows you to write, run, and debug code in your browser. It integrates with AWS services and provides a seamless development experience.