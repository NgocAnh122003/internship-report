---
title: "Resource Clean-up"
date: 2026-09-22
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
aliases:
  - /5-workshop/5.5-cleanup/
---

# 5.5. Resource Clean-up

After successfully building, testing, and operating the Serverless system, we must perform system clean-up to comply with Cloud Best Practices regarding cost optimization and resource lifecycle management.

### Clean-up Steps

#### 1. Delete Amazon S3 Bucket

1. Navigate to the **Amazon S3** service on the AWS Console to view the bucket list.
2. Select the bucket `cloud-note-app-ngocanh-2026`. Because the bucket contains static files, AWS will display a protection warning `This bucket is not empty`, requiring you to empty the data before deletion.

<p align="center">
  <img src="/images/5-Workshop/img_A/40.png" width="850" />
  <br>
  <i>Figure 5.5a: Overview of the Amazon S3 Buckets list</i>
</p>

<p align="center">
  <img src="/images/5-Workshop/img_A/41.png" width="850" />
  <br>
  <i>Figure 5.5b: Selecting the S3 bucket to clean up</i>
</p>

<p align="center">
  <img src="/images/5-Workshop/img_A/42.png" width="850" />
  <br>
  <i>Figure 5.5c: Data protection warning for non-empty buckets</i>
</p>

#### 2. Delete AWS Lambda Function

1. Navigate to the **AWS Lambda** service and check the functions list.
2. Select the `NoteHandler` function and click **Delete function**. Enter the confirmation keyword `confirm` to finalize the deletion.

<p align="center">
  <img src="/images/5-Workshop/img_A/43.png" width="850" />
  <br>
  <i>Figure 5.5d: AWS Lambda Functions list</i>
</p>

<p align="center">
  <img src="/images/5-Workshop/img_A/44.png" width="850" />
  <br>
  <i>Figure 5.5e: Confirmation interface for deleting the NoteHandler Lambda function</i>
</p>

#### 3. Delete Amazon API Gateway

1. Navigate to the **Amazon API Gateway** service and select the HTTP API named `NoteAPI`.
2. Open the API deletion option and enter the confirmation keyword `confirm` into the text box.

<p align="center">
  <img src="/images/5-Workshop/img_A/45.png" width="850" />
  <br>
  <i>Figure 5.5f: Confirmation interface for deleting the NoteAPI HTTP API</i>
</p>

#### 4. Delete Amazon DynamoDB Table

1. Navigate to the **DynamoDB** service and select the **Tables** section.
2. Choose the `NotesTable` table, click **Delete**, and enter `confirm` to delete the table along with its data.

<p align="center">
  <img src="/images/5-Workshop/img_A/46.png" width="850" />
  <br>
  <i>Figure 5.5g: Confirmation interface for deleting the DynamoDB NotesTable</i>
</p>

#### 5. Delete CI/CD IAM User

1. Navigate to the **IAM** service and select the **Users** section to manage identities.
2. Select the `github-actions-bot` user, click **Delete user**, and enter `confirm` to revoke GitHub Actions access.

<p align="center">
  <img src="/images/5-Workshop/img_A/48.png" width="850" />
  <br>
  <i>Figure 5.5h: IAM Users list after governance check</i>
</p>

<p align="center">
  <img src="/images/5-Workshop/img_A/47.png" width="850" />
  <br>
  <i>Figure 5.5i: Confirmation interface for deleting the github-actions-bot IAM User</i>
</p>

#### 6. Clean up Monitoring & Alerts (CloudWatch & SNS)

Do not forget to delete the operational resources configured in the Backend section to avoid hidden costs.

1. Navigate to **CloudWatch**, go to **Dashboards**, and delete the `CloudNote-Monitor` dashboard.
2. Switch to **Alarms**, select the `NoteHandler-Error-Alarm`, click the Actions dropdown, and press **Delete**.
3. Navigate to the **Amazon SNS** service, go to **Topics**, select the alert email topic created for the project, and delete it.

#### 7. Clean up AWS Budgets

1. Navigate to the **Billing and Cost Management** console, then select **Budgets**.
2. Choose the cost control budget created in section 5.1, click **Delete**, and confirm to completely remove the financial alert.

{{% notice success %}}
**Project Completed!**
Congratulations on successfully deploying a comprehensive Serverless architecture. From an automated CI/CD Frontend and a high-speed NoSQL Backend to enterprise-standard monitoring, alerting, and operations systems!
{{% /notice %}}
