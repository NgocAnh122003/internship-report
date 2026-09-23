---
title: "Proposal"
date: 2026-09-21
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Cloud Note App on AWS

## Cost-Optimized Serverless Web Architecture with Zero-Ops

### 1. Executive Summary

This proposal presents an architectural solution for a **Single-Page Application (SPA)** running entirely on the AWS cloud infrastructure. Instead of deploying traditional Virtual Machines (EC2) that require 24/7 maintenance costs and operating system administration effort, the system thoroughly adopts the **Serverless** principle.

The entire user interface (Frontend) is distributed at high speed via **Amazon S3**. Backend processing logic and database operations are triggered completely automatically through **Amazon API Gateway** and **AWS Lambda**. This architecture ensures that the system can automatically scale instantly when traffic spikes, while optimizing budget through a pay-per-millisecond execution model. Furthermore, the source code release process is fully automated (CI/CD) via **GitHub Actions**, delivering a true Zero-Ops operational experience for developers.

---

### 2. Problem Statement

#### Current Challenges

- **High Idle Cost**: Traditional web applications typically require virtual servers (EC2/Virtual Machines) running continuously 24/7 to remain ready for requests, wasting substantial resources during off-peak hours.
- **Single Point of Failure**: Deploying on a single server makes the system vulnerable to overload or complete downtime during traffic spikes, requiring complex Auto Scaling and Load Balancer setups.
- **Operational Overhead**: Continuous operating system updates, security patching, physical database management, and time-consuming manual source code deployments are required.

#### Proposed Solution

The system is designed around a core principle: **Comprehensive Serverless & GitOps**. It completely decouples the Frontend interface layer and the Backend logic layer into independent, fully managed AWS services.

The architecture consists of 4 independent processing flows:

1. **Flow F (Frontend Delivery)**: Static source code (HTML/CSS/JS) is stored and distributed directly from Amazon S3, completely eliminating the need for a traditional Web Server (such as Apache/Nginx).
2. **Flow A (API & Compute)**: The user's browser makes direct calls to Amazon API Gateway. The gateway acts as an entry routing point, triggering AWS Lambda functions (written in Python/Node.js) to handle business logic such as creating, reading, or deleting notes.
3. **Flow D (Data Storage)**: AWS Lambda communicates with Amazon DynamoDB (NoSQL database) to store note contents. DynamoDB automatically partitions data across multiple Availability Zones (AZs) to ensure high availability.
4. **Flow C (CI/CD Pipeline)**: The deployment process is fully automated. When source code changes are pushed to the `main` branch on GitHub, GitHub Actions automatically authenticates and synchronizes the new files to the S3 Bucket, completely eliminating manual upload operations.

#### Benefits and Return on Investment (ROI)

- **Maximum Cost Optimization (up to 95%)**: No idle servers. Fully leverage the AWS Free Tier for S3, Lambda (1 million requests/month), and DynamoDB (25GB storage) during the development phase.
- **Zero-Ops**: AWS takes full responsibility for infrastructure maintenance, OS patching, and software updates, allowing 100% focus on application feature development.
- **Instant Scaling**: Capability to serve from a single user to tens of thousands of concurrent users without manual configuration intervention.

---

### 3. Solution Architecture

#### Overall Architecture Diagram

![Serverless Note App Architecture](/images/2-Proposal/a.png)

#### Detailed Core Processing Flows:

##### 1. Frontend & Static Storage Layer

- Application source code (including `index.html`, CSS styles, and JavaScript logic) is stored in an **Amazon S3 Bucket**.
- The bucket is configured with Static Website Hosting and a Bucket Policy allowing Public Read access so users can access it via the S3 URL.

##### 2. API & Compute Layer

- **Amazon API Gateway** provides a secure REST API endpoint. API Gateway is configured with CORS (Cross-Origin Resource Sharing) to allow external domains (S3) to invoke HTTP POST/GET methods.
- Whenever a request to submit a new note arrives, API Gateway triggers an **AWS Lambda** function. The Lambda function handles parsing JSON data from the Frontend, generating a unique ID (UUID) for the note, and preparing the payload.

##### 3. Database Layer

- The Lambda function uses the AWS SDK (Boto3 for Python) to call the `put_item` method and write data directly to an **Amazon DynamoDB** table.
- The DynamoDB table uses a Single-Table design with the Partition Key as `id` (String), ensuring millisecond-level data access speeds at any scale.

#### AWS Services & Tools Used

- **Amazon S3**: Stores and distributes static web assets.
- **Amazon API Gateway**: REST API entry point, managing CORS and request routing.
- **AWS Lambda**: Executes serverless backend processing logic.
- **Amazon DynamoDB**: Fast and flexible NoSQL database storing note texts.
- **AWS IAM**: Strict permission management, granting roles that allow Lambda to access DynamoDB and generating Access Keys for GitHub Actions to interact with S3.
- **Amazon CloudWatch**: Records Lambda execution logs for monitoring and debugging.
- **GitHub Actions**: CI/CD automation platform, executing workflows to automatically push source code from the repository to the Cloud environment.

---

### 4. Technical Implementation

#### Implementation Phases (Executed in Week 8)

1. **Phase 1: Database Design & Permissions**
   - Create DynamoDB table `NotesTable` with primary key `id`.
   - Create an IAM Execution Role granting `AmazonDynamoDBFullAccess` and `AWSLambdaBasicExecutionRole` permissions to the processing function.
2. **Phase 2: Backend Logic Development**
   - Initialize the AWS Lambda function (`SaveNoteFunction`).
   - Develop Python/Boto3 source code to process incoming event data and write to the database.
3. **Phase 3: API Gateway Integration**
   - Create a public REST API, linking the POST method to the newly created Lambda function.
   - Enable and configure CORS to prevent browser security errors, then deploy the API to a production stage.
4. **Phase 4: S3 Hosting & CI/CD Automation (GitOps)**
   - Create an Amazon S3 Bucket, enable Public Static Website Hosting, and configure the Bucket Policy.
   - Build the HTML/JS interface, embedding the API Gateway URL into the JavaScript source code (Fetch API).
   - Set up the **GitHub Actions** workflow (`main.yml`). Configure secure environment variables (`AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`) in GitHub Secrets to auto-deploy the Frontend source code to S3 upon every Commit/Push.
5. **Phase 5: Testing & Evaluation**
   - Perform CI/CD Pipeline testing.
   - Perform End-to-End data flow testing (Web Interface -> API -> Lambda -> Database).
   - Monitor and analyze logs via CloudWatch.

#### Technical & Security Requirements

- **Least Privilege**: Strict privilege separation via AWS IAM. The Lambda function only has write permissions to the project's specific DynamoDB table. The IAM keys provided to GitHub Actions are also strictly limited to the designated S3 Bucket.
- **Network Resource Governance**: Securing the API Gateway endpoint by tightly controlling Access-Control-Allow-Origin (CORS).
- **Data Protection**: Data transmitted between the Frontend and API Gateway is automatically encrypted via AWS-provided HTTPS protocols.

---

### 5. Implementation Roadmap

```text
+-----------------------------------------------------------------------------------+
| Phase 1: Database Initialization & Backend Logic Development                      |
|   - Create Amazon DynamoDB table.                                                 |
|   - Write AWS Lambda source code for data handling and assign IAM permissions.    |
+-----------------------------------------------------------------------------------+
                                   |
                                   v
+-----------------------------------------------------------------------------------+
| Phase 2: API Routing & Data Flow Integration                                      |
|   - Set up Amazon API Gateway & configure Lambda Integration Request.             |
|   - Enable CORS and Deploy API to retrieve the End-point URL.                     |
+-----------------------------------------------------------------------------------+
                                   |
                                   v
+-----------------------------------------------------------------------------------+
| Phase 3: Frontend Deployment, S3 Hosting & CI/CD Automation                       |
|   - Update API URL in Frontend JavaScript source code.                            |
|   - Create Amazon S3 Bucket, grant Public Read permissions, and host Static Site. |
|   - Set up GitHub Actions Pipeline to auto-deploy source code to S3.              |
+-----------------------------------------------------------------------------------+
```

### 6. Budget Estimation

Despite powerful scalability, the Serverless architecture delivers superior cost efficiency through a Pay-as-you-go model. Below is an estimated monthly budget assuming a Production environment with approximately **5 million interactions/month**:

| AWS Service              | Assumed Scale (Production)                 | Estimated Monthly Cost (USD) |
| :----------------------- | :----------------------------------------- | :--------------------------- |
| **Amazon API Gateway**   | 5,000,000 REST API requests                | ~$5.00                       |
| **AWS Lambda**           | 5,000,000 requests, 128MB RAM, 200ms/req   | ~$1.20                       |
| **Amazon DynamoDB**      | On-Demand Mode, 10GB data, 5M Write/Read   | ~$3.50                       |
| **Amazon S3**            | 20GB static assets storage, 50GB Bandwidth | ~$4.50                       |
| **Amazon Route 53**      | DNS Host Zone Management (1 Domain)        | ~$0.50                       |
| **Amazon CloudWatch**    | Storage for 5GB execution log data         | ~$2.50                       |
| **Total Estimated Cost** | **Serverless system serving 5M requests**  | **~$17.20 USD / Month**      |

> [!TIP]
> **Superior Cost Optimization Advantage**:
> If deployed using a legacy model (requiring at least 2 EC2 virtual servers and 1 RDS Database running 24/7 to handle 5 million requests), fixed costs would range around **$45 - $60/month**. The Serverless model saves up to **70% of the budget** while completely eliminating OS management burdens and automatically preventing traffic crashes under heavy load.

---

### 7. Risk Assessment

#### Risk Matrix & Mitigation Strategy

| Potential Risk                         | Impact Level | Probability | Mitigation Strategy                                                                                                                          |
| :------------------------------------- | :----------: | :---------: | :------------------------------------------------------------------------------------------------------------------------------------------- |
| **Cross-Origin (CORS) Browser Errors** |     High     |    High     | Verify Enable CORS configuration on API Gateway. Ensure the Lambda function returns the proper `Access-Control-Allow-Origin` header in JSON. |
| **Lambda Failure to Write Data to DB** |     High     |   Medium    | Review Lambda IAM Role, ensuring the `AmazonDynamoDBFullAccess` policy is correctly attached.                                                |
| **API Endpoint Information Exposure**  |    Medium    |     Low     | API Gateway includes default request throttling mechanisms. Usage Plans and API Keys can be added for stricter access control if needed.     |

---

### 8. Expected Outcomes

- **Mastery of Modern Cloud Technology**: Successfully built a complete web product from scratch, proficiently applying 4 core AWS services (S3, API Gateway, Lambda, DynamoDB).
- **Demonstrating Serverless Superiority**: Practically proved the pay-as-you-go model, completely eliminating physical infrastructure management burdens and allowing the application to auto-scale dynamically.
- **DevOps/GitOps Operational Mindset**: Successfully integrated a CI/CD pipeline, transforming an academic project into a system with a professional release process comparable to an enterprise environment.
- **Solid Foundation for the Future**: This demo project serves as an accurate architectural blueprint, easily extensible with complex features (such as Amazon Cognito authentication or Amazon SES email dispatch) for future commercial software systems.
