---
title: "Provisioning DynamoDB"
date: 2026-09-22
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
aliases:
  - /5-workshop/5.2-cognito-dynamodb/
---

# 5.2. Provisioning Amazon DynamoDB Database

In this chapter, we will configure a high-performance NoSQL database on **Amazon DynamoDB** to store note content for the application.

### Objectives

- Create a fully serverless DynamoDB table.
- Set up a Partition Key to optimize query speed by unique identifier.
- Verify the database's active status ready for AWS Lambda connection.

---

### Step-by-Step Instructions on AWS Console

#### Step 1: Access DynamoDB Console

1. In the search bar of the [AWS Management Console](https://console.aws.amazon.com/), type **DynamoDB** and select the **Amazon DynamoDB** service.
2. On the left navigation pane, select **Tables** and click the **Create table** button in the top right corner.

#### Step 2: Configure Table Details

Enter the exact technical specifications for the notes table as follows:

- **Table name**: `NotesTable`
- **Partition key**: `id` and select the data type as **String**. This key will store the universally unique identifier (UUID) for each created note.
- **Sort key**: Leave empty, as we are designing a simple table model.

> [!NOTE]
> **Technical Explanation**: DynamoDB's NoSQL model allows the system to operate without pre-defining complex data columns. Using `id` as the Partition Key ensures read/write speeds remain at millisecond latency even when the number of records reaches millions.

#### Step 3: Table Settings and Finalization

1. Under **Table settings**, keep the default option **Default settings** (This uses On-demand capacity mode to automatically adjust resources based on actual traffic, optimizing costs).
2. Scroll to the bottom of the page and click **Create table**.

#### Step 4: Verify Table Status

1. Wait about 5 to 10 seconds for AWS to provision the resource.
2. When the table's Status changes from _Creating_ to **Active**, it means the `NotesTable` table is ready for operation.

<p align="center">
  <img src="/images/5-Workshop/img_A/1.png" width="850" />
  <br>
  <i>Figure 5.2: Successfully provisioned NotesTable on DynamoDB with Active status</i>
</p>

---

### Next Steps

The NoSQL database has been successfully provisioned. Now we will move to **Module 5.3** to build the Backend logic tier with **AWS Lambda** and configure the communication gateway **Amazon API Gateway**.
