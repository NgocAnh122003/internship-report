---
title: "Lambda & API Gateway"
date: 2026-09-22
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
aliases:
  - /5-workshop/5.3-matchmaker-api/
---

# 5.3. Building Serverless Backend with AWS Lambda & API Gateway

In this chapter, we will build the business logic tier using **AWS Lambda** and expose a communication endpoint via **Amazon API Gateway** to connect the Frontend with the Database.

### Objectives

- Provision an AWS Lambda function to handle CRUD operations for notes.
- Deploy Python source code and grant secure IAM Policies to interact directly with the `NotesTable`.
- Deploy Amazon API Gateway (HTTP API) and automatically route connections to the Lambda function.

---

### Step-by-Step Instructions

#### Step 1: Provision AWS Lambda Backend Function

1. Access the **AWS Lambda** service on the AWS Console and select **Create a function**.
2. Choose **Author from scratch** and configure the following parameters:
   - **Function name**: `NoteHandler`
   - **Runtime**: Select `Python 3.12` (or equivalent version).
3. Click **Create function**.

<p align="center">
  <img src="/images/5-Workshop/img_A/2.png" width="850" />
  <br>
  <i>Figure 5.3a: Successfully provisioned AWS Lambda function named NoteHandler</i>
</p>

#### Step 2: Deploy Logic Source Code

1. On the newly created function's interface, scroll down to the **Code source** section.
2. Open the `lambda_function.py` file, and paste the following Python code (note that the table name `NotesTable` is configured within the code):

```python
import json
import boto3

dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('NotesTable')

def lambda_handler(event, context):
    try:
        # Retrieve all notes from DynamoDB
        response = table.scan()
        items = response.get('Items', [])

        return {
            'statusCode': 200,
            'headers': {
                'Access-Control-Allow-Origin': '*',
                'Access-Control-Allow-Headers': 'Content-Type',
                'Access-Control-Allow-Methods': 'OPTIONS,POST,GET,DELETE'
            },
            'body': json.dumps(items)
        }
    except Exception as e:
        return {
            'statusCode': 500,
            'headers': {
                'Access-Control-Allow-Origin': '*',
                'Access-Control-Allow-Headers': 'Content-Type'
            },
            'body': json.dumps({'error': str(e)})
        }
```

#### Step 3: Grant IAM Permissions for Lambda (Least Privilege)

For Lambda to have read/write access to DynamoDB, we must grant permissions to its Execution Role.

1. Switch to the **Configuration** tab $\rightarrow$ select **Permissions**.
2. Click on the Role name link (e.g., `NoteHandler-role-xxxx`) to open the IAM management console.
3. In the IAM Role interface, click **Add permissions** $\rightarrow$ select **Attach policies**.
4. Search for and select the `AmazonDynamoDBFullAccess` policy, then click **Add permissions**.

<p align="center">
  <img src="/images/5-Workshop/img_A/7.png" width="850" />
  <br>
  <i>Figure 5.3c: Successfully granted DynamoDB access permissions for the Lambda Role</i>
</p>

#### Step 4: Create and Configure Amazon API Gateway (HTTP API)

1. Access the **Amazon API Gateway** service and click **Create API**.
2. On the **HTTP API** card, click **Build**.
3. In the _Configure API_ step, set the following parameters:
   - **API name**: `NoteAPI`
   - **Integrations**: Select _Lambda_, point to the current Region, and choose the `NoteHandler` function.
4. Click **Next** to proceed to the _Configure routes_ step. Here, keep or configure the `ANY` method for the `/{proxy+}` resource path.
5. Continue clicking **Next** through the Stage configuration (leave the default `$default`), then click **Review and create** $\rightarrow$ **Create**.

<p align="center">
  <img src="/images/5-Workshop/img_A/13.png" width="850" />
  <br>
  <i>Figure 5.3d: Integration configuration routing all requests to the Lambda function</i>
</p>

6. Once the API Gateway indicates successful creation, navigate to the **Stages** section on the left menu. Copy the provided **Invoke URL**. You will use this URL to configure the Frontend environment variables in the next step.

<p align="center">
  <img src="/images/5-Workshop/img_A/15.png" width="850" />
  <br>
  <i>Figure 5.3e: Successfully deployed API Gateway and retrieved the Invoke URL</i>
</p>

#### Step 5: End-to-End Test

Before deploying the Frontend, we need to ensure the Backend is fully operational.

1. Using the Frontend web application (running locally or on S3), input the API Gateway URL into the configuration.
2. Try entering a note (e.g., "TOI LA QUAN DANG YEU") and click **Save Note**.
3. If the application successfully displays the list of notes, it proves that the `Frontend -> API Gateway -> Lambda -> DynamoDB` data flow is completely seamless.

<p align="center">
  <img src="/images/5-Workshop/img_A/16.png" width="850" />
  <br>
  <i>Figure 5.3f: Frontend interface successfully displaying notes loaded from the Backend</i>
</p>

---

### Next Steps

The processing Backend and API Gateway have been securely completed. Now we move to **Module 5.4** to deploy the user interface to **Amazon S3** and set up the GitOps CI/CD automation workflow with GitHub Actions.
