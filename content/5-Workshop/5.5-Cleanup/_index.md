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

After completing the construction, testing, and operation of the Serverless system, to comply with AWS Best Practices for cost optimization and resource lifecycle management, we perform the resource cleanup steps.

### Clean-up Steps

#### 1. Delete Amazon S3 Bucket

1. Access the **Amazon S3** service on the AWS Console to review the bucket list.
2. Select the bucket `cloud-note-app-ngocanh-2026`. Because the bucket contains static files, AWS displays a protective warning `This bucket is not empty` requiring data clearance before deletion.

<p align="center">
  <img src="/images/5-Workshop/img_A/40.png" width="850" />
  <br>
  <i>Figure 5.5a: Overview of the Amazon S3 Buckets list</i>
</p>

<p align="center">
  <img src="/images/5-Workshop/img_A/41.png" width="850" />
  <br>
  <i>Figure 5.5b: Selecting the S3 bucket for cleanup</i>
</p>

<p align="center">
  <img src="/images/5-Workshop/img_A/42.png" width="850" />
  <br>
  <i>Figure 5.5c: Data protection warning when the bucket is not empty</i>
</p>

#### 2. Delete AWS Lambda Function

1. Access the **AWS Lambda** service to check the list of functions.
2. Select the `NoteHandler` function and execute the **Delete function** command. Enter the confirmation keyword `confirm` to finalize the deletion request.

<p align="center">
  <img src="/images/5-Workshop/img_A/43.png" width="850" />
  <br>
  <i>Figure 5.5d: List of AWS Lambda Functions</i>
</p>

<p align="center">
  <img src="/images/5-Workshop/img_A/44.png" width="850" />
  <br>
  <i>Figure 5.5e: Confirmation interface for deleting Lambda function NoteHandler</i>
</p>

#### 3. Delete Amazon API Gateway

1. Access the **Amazon API Gateway** service, select the HTTP API named `NoteAPI`.
2. Open the API deletion option and enter the confirmation keyword `confirm` into the input field.

<p align="center">
  <img src="/images/5-Workshop/img_A/45.png" width="850" />
  <br>
  <i>Figure 5.5f: Confirmation interface for deleting HTTP API NoteAPI</i>
</p>

#### 4. Delete Amazon DynamoDB Table

1. Access the **DynamoDB** service, select **Tables**.
2. Select the `NotesTable` table, click **Delete**, and enter confirmation `confirm` to delete the table and its data.

<p align="center">
  <img src="/images/5-Workshop/img_A/46.png" width="850" />
  <br>
  <i>Figure 5.5g: Confirmation interface for deleting DynamoDB table NotesTable</i>
</p>

#### 5. Delete CI/CD IAM User

1. Access the **IAM** service, select **Users** to manage identity accounts.
2. Select the `github-actions-bot` user, click **Delete user**, and enter confirmation `confirm` to revoke GitHub Actions access permissions.

<p align="center">
  <img src="/images/5-Workshop/img_A/48.png" width="850" />
  <br>
  <i>Figure 5.5h: IAM Users list after governance check</i>
</p>

<p align="center">
  <img src="/images/5-Workshop/img_A/47.png" width="850" />
  <br>
  <i>Figure 5.5i: Confirmation interface for deleting IAM User github-actions-bot</i>
</p>

---
