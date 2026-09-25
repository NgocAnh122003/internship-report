---
title: "S3 Hosting & GitOps CI/CD"
date: 2026-09-22
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
aliases:
  - /5-workshop/5.4-s3-hosting-gitops/
---

# 5.4. Deploy Frontend to Amazon S3 & Automate with GitOps

In this chapter, we will turn an **Amazon S3** bucket into a static web server (Static Website Hosting) to serve the Frontend interface. Additionally, we will configure a **GitOps CI/CD** pipeline via GitHub Actions so that whenever there is a source code change, the interface will automatically update on S3.

### Objectives

- Configure an Amazon S3 Bucket with a Bucket Policy that allows public access.
- Deploy the HTML/JS source code of the application.
- Initialize a GitHub Repository and set up an automated CI/CD pipeline using GitHub Actions.

---

### Detailed Implementation Steps

#### Step 1: Create an Amazon S3 Bucket

1. Navigate to the **Amazon S3** service on the AWS Console and click **Create bucket**.
2. Configure the basic parameters:
   - **Bucket name**: `cloud-note-app-ngocanh-2026` (The bucket name must be globally unique).
   - **AWS Region**: Select `us-east-1` (N. Virginia).
3. Under **Block Public Access settings for this bucket**, uncheck _Block all public access_ to allow external Internet users to access the website. Acknowledge the risk when prompted.
4. Click **Create bucket**.

<p align="center">
  <img src="/images/5-Workshop/img_A/21.png" width="850" />
  <br>
  <i>Figure 5.4a: Successfully created S3 Bucket named cloud-note-app-ngocanh-2026</i>
</p>

#### Step 2: Configure Static Website Hosting and Bucket Policy

1. Access the newly created bucket and select the **Properties** tab. Scroll down to the **Static website hosting** section, click **Edit**, and choose **Enable**. Set the _Index document_ to `index.html`.
2. Switch to the **Permissions** tab, scroll down to **Bucket policy**, and click **Edit**. Paste the following JSON code to grant public read access (PublicReadGetObject) for all files in the bucket:

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

#### Step 3: Upload HTML File to S3 and Test Hosting

1. Select the **Objects** tab, click **Upload**, and upload your `index.html` file (which has been configured with the API Gateway Invoke URL from the previous step) to the bucket[cite: 27].

<p align="center">
  <img src="/images/5-Workshop/img_A/24.png" width="850" />
  <br>
  <i>Figure 5.4c: Successfully uploaded the interface file (index.html) to the S3 Bucket</i>
</p>

2. Return to the **Properties** tab, scroll down to the **Static website hosting** section, and click the **Bucket website endpoint** link[cite: 27].
3. The browser will open the note management application interface. At this point, the website is officially public on the Internet[cite: 27]!

<p align="center">
  <img src="/images/5-Workshop/img_A/58.png" width="850" />
  <br>
  <i>Figure 5.4d: Modern Minimalist Cloud Note App running successfully on Amazon S3</i>
</p>

{{% notice info %}}
**Experience Optimization:**
The application interface is designed in a modern, minimalist style. Add/Delete actions are called directly via API Gateway to the Serverless Backend, providing near-instant response times.
{{% /notice %}}

#### Step 4: Setup GitHub Repository for GitOps Pipeline

To automate the deployment of code to S3 upon updates, we will utilize GitHub.

1. Go to your GitHub profile (`NgocAnh122003`) and click **New** to create a new Repository.
2. Set the **Repository name** to `cloud-note-app`, choose **Public**, and click **Create repository**.

<p align="center">
  <img src="/images/5-Workshop/img_A/29.png" width="850" />
  <br>
  <i>Figure 5.4e: Initialized the cloud-note-app Repository on GitHub</i>
</p>

3. Upload your `index.html` file to this repository (Commit changes).

<p align="center">
  <img src="/images/5-Workshop/img_A/28.png" width="850" />
  <br>
  <i>Figure 5.4f: Pushed frontend source code to the GitHub Repository</i>
</p>

---

#### Step 5: Configure IAM User and Retrieve Access Key

For GitHub Actions to have permission to push code to S3, a dedicated IAM account is required.

1. Navigate to the **IAM** service on the AWS Console => select **Users** => click **Create user**.
2. Set the user name to `github-actions-bot`.

<p align="center">
  <img src="/images/5-Workshop/img_A/31.png" width="850" />
  <br>
  <i>Figure 5.4g: Created an IAM user named github-actions-bot</i>
</p>

3. In the _Set permissions_ step, select **Attach policies directly** and assign the `AmazonS3FullAccess` policy.

<p align="center">
  <img src="/images/5-Workshop/img_A/32.png" width="850" />
  <br>
  <i>Figure 5.4h: Granted AmazonS3FullAccess permissions to the IAM user</i>
</p>

4. Complete the user creation, then select the newly created user => go to the **Security credentials** tab => click **Create access key**.
5. Choose **Third-party service**, confirm the terms, and retrieve the **Access key** and **Secret access key** pair.

<p align="center">
  <img src="/images/5-Workshop/img_A/34.png" width="850" />
  <br>
  <i>Figure 5.4i: Selected use case to create Access Keys for a third-party service</i>
</p>

#### Step 6: Configure Secure GitHub Secrets

1. Return to the `cloud-note-app` repository on GitHub (`NgocAnh122003`) -> select the **Settings** tab -> **Secrets and variables** -> **Actions**.

2. Click **New repository secret** to sequentially add 2 security variables:
   - Name: `AWS_ACCESS_KEY_ID` | Value: _(Paste your Access Key)_
   - Name: `AWS_SECRET_ACCESS_KEY` | Value: _(Paste your Secret Key)_

<p align="center">
  <img src="/images/5-Workshop/img_A/36.png" width="850" />
  <br>
  <i>Figure 5.4j: Successfully set up AWS Secrets on the GitHub repository</i>
</p>

#### Step 7: Configure GitHub Actions Automation (CI/CD)

1. In the GitHub repository, create a `.github/workflows/` directory and a `main.yml` file.
2. Paste the automation workflow configuration to sync code to S3 whenever there is a `push` to the `main` branch:

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

      - name: 3. Auto Deploy to S3 Bucket
        run: |
          aws s3 cp index.html s3://cloud-note-app-ngocanh-2026/index.html
```

<p align="center">
  <img src="/images/5-Workshop/img_A/37.png" width="850" />
  <br>
  <i>Figure 5.4k: Content of the GitHub Actions main.yml configuration file</i>
</p>

3. Click **Commit changes** to save. The GitHub Actions system will automatically trigger the pipeline.
4. Switch to the **Actions** tab on GitHub to monitor the deployment status. When a green checkmark (`Success`) appears, the GitOps automation is complete!

<p align="center">
  <img src="/images/5-Workshop/img_A/38.png" width="850" />
  <br>
  <i>Figure 5.4l: GitHub Actions pipeline ran successfully (Success)</i>
</p>

{{% notice success %}}
**The Power of CI/CD:**
Instead of risky manual deployments via the AWS Console (ClickOps), the system is now fully automated. Whenever an engineer pushes new source code to the `main` branch, the application interface is updated on S3 in under a minute.
{{% /notice %}}

---

### Next Step

The deployment of the 3-Tier Serverless architecture and the automated CI/CD pipeline are complete. In the final section (**Section 5.5**), we will proceed with resource clean-up to optimize Cloud infrastructure costs.
