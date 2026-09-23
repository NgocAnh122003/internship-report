---
title: "Prerequisites & Region Setup"
date: 2026-09-22
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
aliases:
  - /5-workshop/5.1-prerequiste/
---

# 5.1. Prerequisites & Region Setup

In this section, we will establish the necessary prerequisites and configure the infrastructure region on AWS to begin building the **Cloud Note App**.

### Prerequisites

1. **AWS Account**: An AWS account with Administrator Access or an IAM user with full permissions for Amazon S3, Amazon API Gateway, AWS Lambda, Amazon DynamoDB, and AWS IAM.
2. **Modern Web Browser**: Google Chrome, Mozilla Firefox, Microsoft Edge, or Safari to access the AWS Management Console.
3. **Development Environment & Source Code**:
   - A code editor (such as Visual Studio Code).
   - A initialized **GitHub repository** to manage frontend source code and set up the CI/CD automation pipeline.
   - API testing tool (optional): Postman or cURL for quick endpoint verification.

---

### Step 1: Console Login & Region Selection

1. Access the [AWS Management Console](https://console.aws.amazon.com/) and log in to your account.
2. In the top-right navigation bar, click on the Region selection menu.
3. Choose a standard Region that fully supports serverless services with low latency, such as:
   - **US East (N. Virginia) - us-east-1** (Recommended for stability and latest feature support).
   - Or **Asia Pacific (Singapore) - ap-southeast-1**.

> [!IMPORTANT]
> **Important Note**: You must stick with the same Region throughout the entire workshop. Switching regions midway will break the linkage between resources such as API Gateway, Lambda, and DynamoDB.

<p align="center">
  <img src="/images/5-Workshop/img_A/0.png" width="350" />
  <br>
  <i>Figure: AWS Console Region Selection interface</i>
</p>

### Next Steps

With the workspace successfully configured, let's move on to **Module 5.2** to provision our NoSQL database using **Amazon DynamoDB**.
