---
title: "Proposal"
date: 2026-09-21
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Cloud Note App on AWS

## Cost-Optimized and Zero-Ops Serverless Web Architecture

### 1. Executive Summary

This proposal presents the architectural solution for a **Single-Page Application (SPA)** running entirely on AWS cloud infrastructure. Instead of deploying on a traditional virtual server model (EC2) that requires 24/7 maintenance costs and OS management efforts, this system strictly adopts the **Serverless** principle.

The entire user interface (Frontend) is delivered at high speed via **Amazon S3**. The business logic processing (Backend) and database operations are triggered fully automatically through **Amazon API Gateway** and **AWS Lambda**. This architecture ensures the system can auto-scale instantly when traffic spikes, while optimizing the budget through a pay-per-millisecond model. Furthermore, the system integrates standard **FinOps** (AWS Budgets) and **GitOps** (GitHub Actions), delivering a secure and true Zero-Ops operational experience.

---

### 2. Problem Statement

#### Current Challenges

- **Idle Cost**: Traditional web applications often maintain virtual machines (EC2) running 24/7 to be ready for requests, causing massive resource waste during off-peak hours.
- **Single Point of Failure**: If deployed on a single server, the system is highly vulnerable to overloads or crashes during traffic surges, requiring complex Auto Scaling and Load Balancer setups.
- **Operational Overhead**: Continuous OS patching, security updates, manual performance monitoring, and time-consuming manual deployments for every new release.

#### Proposed Solution

The system is designed around core principles: **Comprehensive Serverless & GitOps**. It completely decouples the Frontend layer and the Backend processing layer into independent, fully AWS-managed services.

The architecture comprises 4 independent data flows:

1.  **Flow F (Frontend Delivery)**: Static UI source code (HTML/CSS/JS) is stored and distributed directly from Amazon S3, completely eliminating the need for a Web Server (like Apache/Nginx).
2.  **Flow A (API & Compute)**: User browsers make direct calls to Amazon API Gateway. The Gateway acts as a routing portal, triggering AWS Lambda functions to handle logic such as creating, reading, or deleting notes.
3.  **Flow D (Data Storage)**: AWS Lambda communicates with Amazon DynamoDB (NoSQL Database) to store note content. DynamoDB automatically replicates data across multiple Availability Zones (AZs) for high availability.
4.  **Flow C (CI/CD Pipeline)**: The UI deployment process is automated using GitHub Actions, automatically syncing new files to the S3 Bucket upon any source code changes.

#### Benefits and Return on Investment (ROI)

- **Maximum Cost Optimization (up to 95%)**: Zero idle servers. Fully utilizes the AWS Free Tier for S3, Lambda, and DynamoDB. Built-in FinOps policy for automated budget alerts.
- **Zero-Ops Experience**: AWS takes full responsibility for infrastructure maintenance. Built-in proactive monitoring and alerting system via CloudWatch.
- **Instant Scalability**: Capable of serving from 1 to tens of thousands of concurrent users without manual configuration intervention.

---

### 3. Solution Architecture

#### Overall Architecture Diagram

![Serverless Note App Architecture](/images/2-Proposal/a.png)

#### Detailed Workflows:

##### 1. Frontend Delivery & Static Web Hosting

- The application source code (`index.html`, CSS styles, and JavaScript logic) is hosted on an **Amazon S3 Bucket**.
- The bucket is configured for Static Website Hosting with a Bucket Policy allowing Public Read.

##### 2. API & Compute Layer

- **Amazon API Gateway** provides a secure REST API Endpoint, configured with CORS to allow external domains to execute HTTP POST/GET/DELETE methods.
- The API Gateway triggers an **AWS Lambda** function responsible for parsing JSON data, generating unique UUIDs, and preparing the payload.

##### 3. Database Layer

- The Lambda function uses the AWS SDK to perform direct interaction methods on the **Amazon DynamoDB** table.
- The DynamoDB table uses a Single-Table design with `id` (String) as the Partition Key.

#### Services & Tools Utilized

- **Amazon S3**: Hosts and delivers the static web interface.
- **Amazon API Gateway**: REST API gateway, managing CORS.
- **AWS Lambda**: Executes serverless Backend logic.
- **Amazon DynamoDB**: High-speed NoSQL database.
- **AWS IAM**: Strict access control management following the Least Privilege principle.
- **Amazon CloudWatch & SNS**: Monitoring system (Dashboard) and automated error alerts (Alarms) via email.
- **AWS Budgets**: Establishes financial guardrails (FinOps) for budget alerts.
- **GitHub Actions**: CI/CD deployment automation platform.

---

### 4. Technical Implementation

#### Project Deployment Phases

1.  **Phase 1: Environment Preparation & Database**
    - Configure **AWS Budgets** to manage cost risks.
    - Provision the DynamoDB `NotesTable` with `id` as the primary key.
    - Create an IAM Execution Role with a Custom Policy restricted to the `NotesTable`.
2.  **Phase 2: Backend Logic Development**
    - Initialize the AWS Lambda function (`NoteHandler`) acting as the central Backend for CRUD operations.
3.  **Phase 3: API Gateway & Monitoring Integration**
    - Create a public REST/HTTP API, link it to the Lambda function, and configure CORS.
    - Build a **CloudWatch Dashboard** for traffic monitoring and set up **CloudWatch Alarms** paired with SNS to trigger alerts during API failures.
4.  **Phase 4: S3 Hosting & CI/CD Automation (GitOps)**
    - Create an Amazon S3 Bucket and enable Public Static Website Hosting.
    - Initialize a **GitHub Actions** workflow (`main.yml`) with secure IAM User variables for automated Frontend deployments to S3.
5.  **Phase 5: Testing, Evaluation & Clean-up**
    - Conduct End-to-End testing of the entire system. Delete resources (Clean-up) to protect the budget.

#### Technical & Security Requirements

- **Least Privilege**: Segregation of duties via AWS IAM.
- **Network Resource Governance**: Secure the API Gateway endpoint using CORS controls.
- **Data Protection**: Automatic encryption in transit via the HTTPS protocol.

---

### 5. Roadmap & Milestones

```text
+-----------------------------------------------------------------------------------+
| Phase 1: FinOps Configuration & Database Initialization                           |
|   - Set up AWS Budgets. Create Amazon DynamoDB table.                             |
+-----------------------------------------------------------------------------------+
                                  |
                                  v
+-----------------------------------------------------------------------------------+
| Phase 2 & 3: Backend, API Gateway & Monitoring Development                        |
|   - Write AWS Lambda logic and route via Amazon API Gateway.                      |
|   - Configure CloudWatch Dashboard & proactive Alarms.                            |
+-----------------------------------------------------------------------------------+
                                  |
                                  v
+-----------------------------------------------------------------------------------+
| Phase 4: Frontend Deployment, S3 Hosting & CI/CD Automation                       |
|   - Provision static S3 Bucket. Configure GitHub Actions for GitOps workflow.     |
+-----------------------------------------------------------------------------------+
```

### 6. Estimated Budget

Below is the estimated monthly cost assuming the system operates in a Production environment with a traffic volume of approximately **5 million interactions/month**:

| AWS Service              | Assumed Scale (Production)                       | Estimated Cost / Month (USD) |
| ------------------------ | ------------------------------------------------ | ---------------------------- |
| **Amazon API Gateway**   | 5,000,000 REST API requests                      | ~$5.00                       |
| **AWS Lambda**           | 5,000,000 requests, 128MB RAM, 200ms/req         | ~$1.20                       |
| **Amazon DynamoDB**      | On-Demand Mode, 10GB data, 5 million Write/Read  | ~$3.50                       |
| **Amazon S3**            | 20GB static assets storage, 50GB Bandwidth       | ~$4.50                       |
| **Amazon Route 53**      | DNS Hosted Zone Management (1 Domain)            | ~$0.50                       |
| **Amazon CloudWatch**    | Store logs & maintain Dashboard/Alarms           | ~$2.50                       |
| **Total Estimated Cost** | **Serverless system serving 5 million requests** | **~$17.20 USD / Month**      |

> **💡 TIP - Outstanding Cost Optimization:**
> If deployed using the legacy model (requiring at least 2 EC2 instances and 1 RDS Database running 24/7), the fixed cost would be around **$45 - $60/month**. The Serverless model saves up to **70% of the budget**. The project is also fully protected by the alerting mechanism of **AWS Budgets**.

---

### 7. Risk Assessment

#### Risk Matrix & Mitigation Strategies

| Potential Risk                               | Impact Level | Probability | Mitigation Strategy                                                                                                                                    |
| -------------------------------------------- | ------------ | ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Unexpected costs**                         | High         | Low         | Set up AWS Budgets to immediately send email alerts if costs approach the forecasted threshold.                                                        |
| **Cross-Origin (CORS) errors from browsers** | High         | High        | Verify Enable CORS configuration on API Gateway and the Response Headers from Lambda.                                                                  |
| **System failures not detected in time**     | High         | Medium      | Set up CloudWatch Alarms to monitor Lambda's Errors metric, integrated with SNS to alert the operations team within 5 minutes.                         |
| **API Endpoint data leak**                   | Medium       | Low         | API Gateway has default throttling mechanisms to prevent request floods. Usage Plans and API Keys can be added to strictly limit access in production. |

---

### 8. Expected Outcomes

- **Mastering Modern Cloud Technologies**: Proficiently apply 4 core AWS services (S3, API Gateway, Lambda, DynamoDB).
- **Enterprise-Grade Operations**: Successfully integrate automated CI/CD pipelines (GitOps), combined with a financial control mindset (FinOps) and proactive system monitoring (Monitoring & Alerting).
- **Validating Serverless Superiority**: Completely eliminate the burden of physical infrastructure management, allowing the application to automatically scale based on actual demand with highly optimized costs.
