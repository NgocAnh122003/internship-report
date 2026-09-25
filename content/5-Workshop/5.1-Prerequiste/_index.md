---
title: "Prerequisites & Region Setup"
date: 2026-09-22
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
aliases:
  - /5-workshop/5.1-prerequiste/
---

# 5.1. Environment Preparation & Region Setup

In this section, we will set up the necessary prerequisites and configure the infrastructure region on AWS to start building the **Cloud Note App**.

### Prerequisites

1. **AWS Account**: Administrator Access or an IAM User with full permissions to interact with the following services: Amazon S3, Amazon API Gateway, AWS Lambda, Amazon DynamoDB, and AWS IAM.
2. **Modern Web Browser**: Google Chrome, Mozilla Firefox, Microsoft Edge, or Safari (latest version) to access the AWS Management Console.
3. **Development Environment & Source Code**:
   - Installed a code editor (such as Visual Studio Code).
   - Initialized a repository on **GitHub** to manage the frontend source code and set up the CI/CD pipeline.
   - API testing tool (optional): Postman or cURL to quickly test API endpoints.

---

### Step 1: Log in to AWS Console and Select Region

1. Access the [AWS Management Console](https://console.aws.amazon.com/) and log in with your account.
2. On the navigation bar at the top right of the screen, click the Region drop-down menu.
3. Choose a standard Region that fully supports Serverless services with low latency, for example:
   - **US East (N. Virginia) - us-east-1** (Recommended for high stability and early feature updates).
   - Or **Asia Pacific (Singapore) - ap-southeast-1**.

> [!IMPORTANT]
> **Important Note**: You must consistently use the same Region throughout this workshop. Changing Regions halfway will break the connections between resources like API Gateway, Lambda, and DynamoDB.

<p align="center">
  <img src="/images/5-Workshop/img_A/0.png" width="300" />
  <br>
  <i>Figure: Region selection interface on the AWS Console</i>
</p>

---

### Step 2: Set up Cost Management (AWS Budgets)

To ensure the system operates 100% safely within the Free Tier limits and to avoid unexpected bills, an automatic budget alert must be configured right from the beginning.

<p align="center">
  <img src="/images/5-Workshop/img_A/49.png" width="800" />
  <br>
  <i>Figure: Setting up cost alerts with AWS Budgets</i>
</p>

{{% notice info %}}
**FinOps Best Practice:**
Any resource created in this project is strictly monitored. The AWS Budgets system will automatically send an alert email to the administrator if the account's Forecasted amount exceeds $100.
{{% /notice %}}

---

### Next Step

After successfully setting up the workspace and safety budget, we will move to **Section 5.2** to provision the NoSQL database with **Amazon DynamoDB**.
