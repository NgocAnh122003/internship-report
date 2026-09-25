---
title: "Lambda & API Gateway"
date: 2026-09-22
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
aliases:
  - /5-workshop/5.3-matchmaker-api/
---

# 5.3. Building AWS Lambda Backend & Amazon API Gateway

In this chapter, we will build the business logic processing layer using **AWS Lambda** and expose a communication endpoint via **Amazon API Gateway** to connect the Frontend with the Database.

### Objectives

- Create an AWS Lambda function to handle CRUD operations for notes.
- Deploy Python source code and grant secure IAM Policy permissions to interact directly with the `NotesTable`.
- Deploy Amazon API Gateway (HTTP API) and route incoming connections automatically to the Lambda function.
- Integrate CloudWatch monitoring (Dashboard & Alarms) to track system health and trigger automated error alerts.

---

### Detailed Steps

#### Step 1: Initialize the AWS Lambda Backend Function

1. Access the **AWS Lambda** service on the AWS Console and select **Create a function**.
2. Choose **Author from scratch** and configure the parameters:
   - **Function name**: `NoteHandler`
   - **Runtime**: Select `Python 3.12` (or equivalent).
3. Click **Create function**.

<p align="center">
  <img src="/images/5-Workshop/img_A/2.png" width="850" />
  <br>
  <i>Figure 5.3a: Successfully created the AWS Lambda function named NoteHandler</i>
</p>

#### Step 2: Develop Logic Processing Source Code (Lambda Code)

1. In the function interface, scroll down to the **Code source** section.
2. Open the `lambda_function.py` file and paste the following Python code (note that the table name `NotesTable` is hardcoded):

```python
import json
import boto3

dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('NotesTable')

def lambda_handler(event, context):
    try:
        # Fetch all notes from DynamoDB
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

#### Step 3: Grant IAM Permissions to Lambda (Least Privilege)

For Lambda to read/write to DynamoDB, we must grant permissions to its Execution Role.

1. Switch to the **Configuration** tab => select **Permissions**.
2. Click the Role name link (e.g., `NoteHandler-role-xxxx`) to open the IAM management interface.
3. In the IAM Role interface, click **Add permissions** => select **Attach policies**.
4. Search for and select the `AmazonDynamoDBFullAccess` policy, then click **Add permissions**.

<p align="center">
  <img src="/images/5-Workshop/img_A/7.png" width="850" />
  <br>
  <i>Figure 5.3c: Successfully granted DynamoDB access permissions to the Lambda Role</i>
</p>

#### Step 4: Create and Configure Amazon API Gateway (HTTP API)

1. Access the **Amazon API Gateway** service and click **Create API**.
2. Under **HTTP API**, click **Build**.
3. In the _Configure API_ step, set the following parameters:
   - **API name**: `NoteAPI`
   - **Integrations**: Select _Lambda_, point to the current Region, and select the `NoteHandler` function.
4. Click **Next** to proceed to the _Configure routes_ step. Leave the method as `ANY` for the resource path `/{proxy+}`.
5. Click **Next** through the Stage configuration (leave default `$default`), then click **Review and create** => **Create**.

<p align="center">
  <img src="/images/5-Workshop/img_A/13.png" width="850" />
  <br>
  <i>Figure 5.3d: Integration configured to route all requests to the Lambda function</i>
</p>

6. Once the API Gateway is successfully created, navigate to **Stages** on the left menu. Copy the provided **Invoke URL**. You will use this URL in the Frontend's environment variables in the next step.

<p align="center">
  <img src="/images/5-Workshop/img_A/15.png" width="850" />
  <br>
  <i>Figure 5.3e: API Gateway deployed successfully showing the Invoke URL</i>
</p>

#### Step 5: End-to-End Data Flow Testing

Before deploying the Frontend, ensure the Backend is functional.

1. Using the Frontend web application (running locally or on S3), input the API Gateway URL into the configuration.
2. Enter a test note and click **Save Note**.
3. If the application successfully displays the list of notes, the `Frontend -> API Gateway -> Lambda -> DynamoDB` data flow is fully operational.

<p align="center">
  <img src="/images/5-Workshop/img_A/16.png" width="850" />
  <br>
  <i>Figure 5.3f: Frontend UI successfully loading notes from the Backend</i>
</p>

---

#### Step 6: Setup Monitoring & Alerts (CloudWatch Operations)

Instead of just deploying and forgetting, a proactive Operations system has been integrated to monitor API health.

**1. Performance Monitoring (CloudWatch Dashboard)**

A centralized dashboard is designed to monitor vital metrics in real time:

- **Traffic & Latency:** Function calls (`Invocations`) and execution time (`Duration`) for Lambda.
- **Database Capacity:** Read/write units (`ConsumedReadCapacityUnits`, `ConsumedWriteCapacityUnits`) for DynamoDB.

<p align="center">
  <img src="/images/5-Workshop/img_A/53.png" width="850" />
  <br>
  <i>Figure 5.3g: CloudWatch Dashboard monitoring the Backend</i>
</p>

**2. Alert System (CloudWatch Alarms)**

To prevent undetected system failures, an automated alert mechanism has been activated.

<p align="center">
  <img src="/images/5-Workshop/img_A/56.png" width="850" />
  <br>
  <i>Figure 5.3h: Automated alert for Lambda function errors</i>
</p>

{{% notice info %}}
**DevOps Best Practice:**
The `NoteHandler-Error-Alarm` is configured to monitor the `Errors` metric. If any errors occur from the Backend (e.g., due to code bugs or DynamoDB overload) over a 5-minute period, the system will immediately send a notification email via Amazon SNS to the operations team.
{{% /notice %}}

---

### Next Step

The backend processing and API gateway have been securely finalized along with the monitoring system. We will now move to **Section 5.4** to deploy the user interface to **Amazon S3** and set up the GitOps CI/CD automation pipeline using GitHub Actions.

```

```
