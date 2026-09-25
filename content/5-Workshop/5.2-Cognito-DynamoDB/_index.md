---
title: "Provisioning DynamoDB"
date: 2026-09-22
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
aliases:
  - /5-workshop/5.2-cognito-dynamodb/
---

# 5.2. Provisioning Amazon DynamoDB

In this chapter, we will configure a high-performance NoSQL database on **Amazon DynamoDB** to store the content of our application's notes.

### Objectives

- Create a fully Serverless DynamoDB table.
- Set up a Partition Key to optimize query speed based on unique identifiers.
- Verify that actual data is successfully stored through the management console.

---

### Steps on the AWS Console

#### Step 1: Access the DynamoDB Console

1. In the search bar of the [AWS Management Console](https://console.aws.amazon.com/), type **DynamoDB** and select the **Amazon DynamoDB** service.
2. In the left navigation pane, choose **Tables** and click the **Create table** button in the top right corner.

#### Step 2: Configure Table Settings

Enter the exact technical specifications for the notes table as follows:

- **Table name**: `NotesTable`
- **Partition key**: `id` and set the data type to **String**. This key will store a unique identifier (UUID) for each generated note.
- **Sort key**: Leave empty, as we are designing a simple table model.

> [!NOTE]
> **Technical Insight**: DynamoDB's NoSQL model means the system does not require pre-defining complex data columns. Using `id` as the Partition Key ensures read/write speeds remain at millisecond latency, even when the record count scales to millions.

#### Step 3: Finalize and Create

1. Under the **Table settings** section, keep the **Default settings** (This utilizes On-demand capacity mode to automatically scale resources based on actual traffic, optimizing costs).
2. Scroll to the bottom of the page and click the **Create table** button.

#### Step 4: Verify Table Status

1. Wait about 5 to 10 seconds for AWS to provision the resource.
2. When the table's Status changes from _Creating_ to **Active**, the `NotesTable` is ready for operations.

<p align="center">
  <img src="/images/5-Workshop/img_A/1.png" width="850" />
  <br>
  <i>Figure 5.2: NotesTable successfully created on DynamoDB with an Active status</i>
</p>

#### Step 5: Verify Actual Data Storage (Explore Items)

Once the application is fully built and operational (in the upcoming steps), you can verify whether the actual data entered by users from the Web UI has been successfully saved into this table.

1. In the top right corner of the table details screen, click the **Explore table items** button.
2. Scroll down to the **Items returned** section; the list of notes will be fully displayed.

<p align="center">
  <img src="/images/5-Workshop/img_A/57.png" width="850" />
  <br>
  <i>Figure 5.3: Actual note data successfully stored in the DynamoDB table</i>
</p>

{{% notice success %}}
**The Flexibility of NoSQL:**
As shown in the image above, even though we only defined the primary key `id` in Step 2, DynamoDB automatically accepts and flawlessly stores new dynamic data fields (such as `content` and `timestamp`) pushed from the Backend API layer.
{{% /notice %}}

---

### Next Step

The NoSQL database has been successfully provisioned and is ready to receive data. We will now move on to **Section 5.3** to build the Backend logic processing layer with **AWS Lambda**, integrate monitoring, and configure the communication gateway via **Amazon API Gateway**.
