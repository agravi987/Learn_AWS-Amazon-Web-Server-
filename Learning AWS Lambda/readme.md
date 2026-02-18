# 📘 S3 Triggered Lambda (Python) – Serverless Event Processing

## 1️⃣ Project Overview

This project demonstrates an event-driven serverless architecture using AWS services. Whenever a file is uploaded to an Amazon S3 bucket, it automatically triggers an AWS Lambda function written in Python. The Lambda function processes the uploaded object (e.g., logs metadata, validates content, or performs transformations).

This architecture eliminates the need for managing servers and ensures automatic scaling.

## 2️⃣ Architecture Diagram (Logical Flow)

```
graph TD
    User[User Uploads File] -->|PUT Object| S3[Amazon S3 Bucket]
    S3 -->|Event Notification| Lambda[AWS Lambda Function (Python)]
    Lambda -->|Log/Transform| CloudWatch[Processing / Logging / Transformation]
```

## 3️⃣ Services Used

- **🔹 Amazon S3**: Used as object storage to store uploaded files. Configured with Event Notification (PUT/Object Created) to trigger Lambda.
- **🔹 AWS Lambda**: Serverless compute service (Python 3.x runtime) triggered automatically by S3 events. Executes stateless logic and follows a stateless execution model.
- **🔹 IAM (Identity and Access Management)**: Provides execution role for Lambda with `AWSLambdaBasicExecutionRole` and S3 read permissions.

## 4️⃣ Implementation Steps

### Step 1: Create S3 Bucket

1.  Go to **AWS Console → S3**.
2.  Create a bucket (e.g., `s3-lambda-trigger-demo`).
3.  Keep default settings (**Block Public Access** enabled).

### Step 2: Create Lambda Function

1.  **Runtime**: Python 3.x
2.  **Execution Role**: Create a new role with basic permissions.
3.  **Code**: Use the following sample Python code.

```python
import json
import urllib.parse
import boto3

def lambda_handler(event, context):
    s3 = boto3.client('s3')

    # Get the bucket name and file key from the event
    bucket = event['Records'][0]['s3']['bucket']['name']
    key = urllib.parse.unquote_plus(event['Records'][0]['s3']['object']['key'])

    print(f"File uploaded: {key}")
    print(f"Bucket name: {bucket}")

    # Fetch the object metadata to get file size
    response = s3.get_object(Bucket=bucket, Key=key)
    print("File Size:", response['ContentLength'])

    return {
        'statusCode': 200,
        'body': json.dumps('File processed successfully!')
    }
```

### Step 3: Configure S3 Trigger

1.  Open your **S3 Bucket**.
2.  Go to **Properties**.
3.  Scroll to **Event Notifications**.
4.  Add a new notification:
    - **Event type**: All object create events.
    - **Destination**: Lambda function.
    - Select your Lambda function.

## 5️⃣ How It Works (Execution Flow)

1.  **Upload**: User uploads a file to S3.
2.  **Trigger**: S3 generates an event notification.
3.  **Invoke**: Lambda receives the event payload (JSON structure).
4.  **Process**: Lambda extracts the bucket name and object key.
5.  **Log**: Lambda processes the file and logs metadata to CloudWatch.

## 6️⃣ Event Payload Structure

> **Important for Interviews**: You must know how to parse `event['Records'][0]`.

S3 sends an event structure similar to this:

```json
{
  "Records": [
    {
      "s3": {
        "bucket": {
          "name": "bucket-name"
        },
        "object": {
          "key": "file-name.txt"
        }
      }
    }
  ]
}
```

## 7️⃣ Key Cloud Concepts Demonstrated

- ✅ **Event-Driven Architecture**
- ✅ **Serverless Computing**
- ✅ **Managed Services**
- ✅ **IAM Role-Based Access**
- ✅ **Auto Scaling**
- ✅ **Pay-per-use Billing**

## 8️⃣ Security Considerations

- Use **least-privilege** IAM policies.
- **Block public access** in S3.
- Enable **bucket versioning** (optional) for data protection.
- Use **CloudWatch Logs** for monitoring and auditing.

## 9️⃣ Cost Consideration

This project typically stays within the **AWS Free Tier**:

- **S3 Storage**: < 5GB per month.
- **Lambda**: < 1M requests per month.
- _Very cost-efficient for students!_ 💰

## 🔟 Possible Enhancements (To Level Up Resume 🚀)

You can extend this project by:

- Writing metadata to **DynamoDB**.
- Sending notifications via **SNS**.
- Implementing image resizing using **Pillow**.
- Adding a virus scanning workflow.
- Using **S3 prefix filters** (e.g., only trigger on `.jpg` files).
- Adding a **Dead Letter Queue (DLQ)** for failed events.
