---
title: "S3 Hosting & GitOps CI/CD"
date: 2026-09-22
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
aliases:
  - /5-workshop/5.4-s3-hosting-gitops/
---

# 5.4. Deploying Frontend to Amazon S3 and Automating with GitOps

In this chapter, we will turn an **Amazon S3** storage bucket into a Static Website Hosting server to serve the Frontend interface. Additionally, we will configure a **GitOps CI/CD** pipeline via GitHub Actions so that whenever the source code changes, the UI automatically updates on S3.

### Objectives

- Configure an Amazon S3 Bucket with a Bucket Policy that allows public access.
- Deploy the application's HTML/JS source code.
- Initialize a GitHub Repository and set up an automated CI/CD pipeline using GitHub Actions.

---

### Step-by-Step Instructions

#### Step 1: Provision Amazon S3 Bucket

1. Access the **Amazon S3** service on the AWS Console and click **Create bucket**.
2. Set the basic parameters:
   - **Bucket name**: `cloud-note-app-ngocanh-2026` (The bucket name must be globally unique).
   - **AWS Region**: Select `us-east-1` (N. Virginia).
3. In the **Block Public Access settings for this bucket** section, uncheck _Block all public access_ to allow external internet users to access the website. Acknowledge the risk when prompted.
4. Click **Create bucket**.

<p align="center">
  <img src="/images/5-Workshop/img_A/21.png" width="850" />
  <br>
  <i>Figure 5.4a: Successfully provisioned S3 Bucket named cloud-note-app-ngocanh-2026</i>
</p>

#### Step 2: Configure Static Website Hosting and Bucket Policy

1. Navigate to the newly created bucket, select the **Properties** tab. Scroll down to the **Static website hosting** section, click **Edit**, and choose **Enable**. Set the _Index document_ to `index.html`.
2. Switch to the **Permissions** tab, scroll down to **Bucket policy**, and click **Edit**. Paste the following JSON snippet to grant public read access (PublicReadGetObject) to all files in the bucket:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::cloud-note-app-ngocanh-2026/*"
    }
  ]
}
```

#### Step 3: Upload HTML file to S3 and Verify Hosting

1. Select the **Objects** tab, click **Upload**, and upload the `index.html` file (which has been configured with the API Gateway Invoke URL in the previous step) to the bucket.

<p align="center">
  <img src="/images/5-Workshop/img_A/24.png" width="850" />
  <br>
  <i>Figure 5.4c: Successfully uploaded the interface file (index.html) to the S3 Bucket</i>
</p>

2. Return to the **Properties** tab, scroll down to the **Static website hosting** section, and click on the **Bucket website endpoint** link.

<p align="center">
  <img src="/images/5-Workshop/img_A/25.png" width="850" />
  <br>
  <i>Figure 5.4d: Retrieving the Amazon S3 Static Website Endpoint URL</i>
</p>

3. The browser will open the note management application interface. The website is now officially public on the Internet!

<p align="center">
  <img src="/images/5-Workshop/img_A/26.png" width="850" />
  <br>
  <i>Figure 5.4e: Cloud Note App successfully running on Amazon S3</i>
</p>

#### Step 4: Set up GitHub Repository for GitOps Workflow

To automate the process of uploading code to S3 upon every update, we use GitHub.

1. Access your personal GitHub page (account `NgocAnh122003`) and click **New** to create a new Repository.

2. Set the **Repository name** to `cloud-note-app`, choose **Public** mode, and click **Create repository**.

<p align="center">
  <img src="/images/5-Workshop/img_A/29.png" width="850" />
  <br>
  <i>Figure 5.4f: Initializing the cloud-note-app repository on GitHub</i>
</p>

3. Upload your `index.html` file to this repository (Commit changes).

<p align="center">
  <img src="/images/5-Workshop/img_A/28.png" width="850" />
  <br>
  <i>Figure 5.4g: Pushing the frontend source code to the GitHub Repository</i>
</p>

---

### Next Steps

Static Hosting is working perfectly, and the source code has been pushed to GitHub. In the next section, we will create an IAM user to retrieve Access Keys and configure **GitHub Actions** to complete the CI/CD pipeline, fully automating the deployment process.

#### Step 5: Configure IAM User and Retrieve Access Keys

To grant GitHub Actions the permission to push code to S3, we need a dedicated IAM account.

1. Access the **IAM** service on the AWS Console $\rightarrow$ select **Users** $\rightarrow$ click **Create user**.
2. Name the user `github-actions-bot`.

<p align="center">
  <img src="/images/5-Workshop/img_A/31.png" width="850" />
  <br>
  <i>Figure 5.4h: Provisioning the IAM user named github-actions-bot</i>
</p>

3. In the _Set permissions_ step, select **Attach policies directly** and search/attach the `AmazonS3FullAccess` policy.

<p align="center">
  <img src="/images/5-Workshop/img_A/32.png" width="850" />
  <br>
  <i>Figure 5.4i: Attaching AmazonS3FullAccess policy to the IAM user</i>
</p>

4. Complete the user creation, select the newly created user $\rightarrow$ tab **Security credentials** $\rightarrow$ click **Create access key**.
5. Choose the **Third-party service** use case, confirm the recommendation, and retrieve the **Access key** and **Secret access key** pair.

<p align="center">
  <img src="/images/5-Workshop/img_A/34.png" width="850" />
  <br>
  <i>Figure 5.4j: Selecting the use case to generate an Access Key for third-party services</i>
</p>

<p align="center">
  <img src="/images/5-Workshop/img_A/35.png" width="850" />
  <br>
  <i>Figure 5.4k: Retrieving the Access Key ID and Secret Access Key credentials</i>
</p>

#### Step 6: Configure Secure GitHub Secrets

1. Return to the `cloud-note-app` repository on GitHub (`NgocAnh122003`) -> select the **Settings** tab -> **Secrets and variables** -> **Actions**.

2. Click **New repository secret** to add two security variables:
   - Name: `AWS_ACCESS_KEY_ID` | Value: _(Paste your Access Key)_
   - Name: `AWS_SECRET_ACCESS_KEY` | Value: _(Paste your Secret Key)_

<p align="center">
  <img src="/images/5-Workshop/img_A/36.png" width="850" />
  <br>
  <i>Figure 5.4l: Successfully configuring AWS Secrets on the GitHub repository</i>
</p>

#### Step 7: Configure GitHub Actions Automation Pipeline (CI/CD)

1. In your GitHub repository, create the `.github/workflows/` directory and add a `main.yml` file.
2. Paste the automation pipeline configuration to synchronize code to S3 on every `push` to the `main` branch:

```yaml
name: Deploy Frontend to Amazon S3

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: 1. Checkout Code
        uses: actions/checkout@v3

      - name: 2. Configure AWS Credentials
        uses: aws-actions/configure-aws-credentials@v2
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: us-east-1

      - name: 3. Automatically Deploy to S3 Bucket
        run: |
          aws s3 cp index.html s3://cloud-note-app-ngocanh-2026/index.html
```

<p align="center">
  <img src="/images/5-Workshop/img_A/37.png" width="850" />
  <br>
  <i>Figure 5.4m: Content of the main.yml configuration file for GitHub Actions</i>
</p>

3. Click **Commit changes** to save. The GitHub Actions system will automatically trigger the workflow execution.

4. Navigate to the **Actions** tab on GitHub to monitor the deployment status. Once a green checkmark (`Success`) appears, the GitOps automation workflow has been successfully completed!

<p align="center">
  <img src="/images/5-Workshop/img_A/38.png" width="850" />
  <br>
  <i>Figure 5.4n: GitHub Actions workflow executed successfully (Success)</i>
</p>

<p align="center">
  <img src="/images/5-Workshop/img_A/39.png" width="850" />
  <br>
  <i>Figure 5.4o: Overview of workflow execution history on GitHub Actions</i>
</p>

---

### Workshop Completed

Congratulations! You have successfully deployed a complete 3-Tier Serverless Architecture application on the AWS Cloud platform, integrated with an automated CI/CD GitOps workflow using GitHub Actions.
