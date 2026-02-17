# ✨ AWS Lambda 

![AWS GIF](https://github.com/shyamdevk/AWS-Concepts-Labs/blob/images/lambda.gif)

## 📘 **Overview**

AWS Lambda is a **serverless compute service** that lets you run code **without managing servers**.
You only upload your function, set a trigger, and Lambda handles the rest—scaling, execution, and availability.

---

## 🚀 **Why Use AWS Lambda?**

* ⚡ **No servers to manage**
* 📈 **Auto-scaling** based on traffic
* 💰 **Pay only for execution time**
* 🔗 **Easy integration** with S3, API Gateway, DynamoDB, CloudWatch, etc.

---

## 🧠 **How Lambda Works**

1. Upload your function (your logic).
2. Set a **trigger/event** to invoke the function.
3. When the event occurs → Lambda executes automatically.
4. Output is generated & logs go to **CloudWatch Logs**.

---

# 🧩 Key Concepts

### 🔹 **Function**

Your code + configuration.

### 🔹 **Trigger (Event Source)**

Something that starts the Lambda. Examples:

* S3 file upload
* DynamoDB table update
* API call through API Gateway
* CloudWatch scheduled cron job

### 🔹 **Execution Role (IAM Role)**

Permission set that allows Lambda to access other AWS services.

### 🔹 **Memory & Timeout**

* Memory: 128MB → 10GB
* Timeout: up to 15 minutes
  Both affect performance and cost.

### 🔹 **Cold Start vs Warm Start**

* ❄️ **Cold Start** → First run after idle (slightly slower)
* 🔥 **Warm Start** → Lambda already running (fast)

---

# 🛠 Supported Runtimes

Lambda supports multiple languages:

* Python
* Node.js
* Java
* Go
* .NET
* Ruby

---

# 💵 Pricing

You pay for:

* Number of requests
* Duration (per millisecond)

**First 1 million requests per month are FREE.**

---

# 🎯 Common Use Cases

* Serverless backend APIs
* Process S3 uploads
* Send emails/notifications
* Cron jobs & automation
* IoT and real-time data processing
* Serverless websites

---
Here is your **clean, simple, beginner-friendly `README.md`** for the S3 → Lambda → CloudWatch Lab.

---

# 🟦 AWS Lambda S3 Trigger Lab

**Goal:** When an object is uploaded to S3, it automatically triggers a Lambda function and logs the event in CloudWatch.

---

## 🔹 **Lab Overview**

You will:

1. Create an S3 bucket
2. Create a Lambda function (Python 3.14)
3. Add Lambda code and deploy
4. Create a role and give access to S3 + CloudWatch
5. Add S3 trigger
6. Test the function
7. Verify logs in CloudWatch

---

# ✅ **1. Create S3 Bucket**

* Go to **S3 Console → Create bucket**
* Bucket name: **`lambda-test-buck-909090`**
* Region: **us-east-1**
* Click **Create bucket**

---

# ✅ **2. Create Lambda Function (Python 3.14)**

* Go to **Lambda Console → Create function**
* Choose **Author from scratch**
* Function name: **`s3-lambda-logger`**
* Runtime: **Python 3.14**
* Click **Create function**

---

# ✅ **3. Add Lambda Code**

Open the function → **Code** → replace default code with:

```python
# Copyright Amazon.com, Inc. or its affiliates. All Rights Reserved.
# SPDX-License-Identifier: Apache-2.0
import json
import urllib.parse
import boto3

print('Loading function')

s3 = boto3.client('s3')


def lambda_handler(event, context):
    #print("Received event: " + json.dumps(event, indent=2))

    # Get the object from the event and show its content type
    bucket = event['Records'][0]['s3']['bucket']['name']
    key = urllib.parse.unquote_plus(event['Records'][0]['s3']['object']['key'], encoding='utf-8')
    try:
        response = s3.get_object(Bucket=bucket, Key=key)
        print("CONTENT TYPE: " + response['ContentType'])
        return response['ContentType']
    except Exception as e:
        print(e)
        print('Error getting object {} from bucket {}. Make sure they exist and your bucket is in the same region as this function.'.format(key, bucket))
        raise e
```

Click **Deploy**

---

# ✅ **4. Set IAM Role Permissions**

### 👉 Step A: Give CloudWatch Logs access

* Go to **Lambda → Configuration → Permissions**
* Click the **Role name**
* Click **Add Permissions → Attach policies**
* Search: **AWSLambdaBasicExecutionRole**
* Click **Attach**

### 👉 Step B: Give S3 Access

* In the same role page → **Add permissions → Create inline policy**
* Choose **JSON** and paste:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["s3:GetObject", "s3:ListBucket"],
      "Resource": [
        "arn:aws:s3:::lambda-test-buck-909090",
        "arn:aws:s3:::lambda-test-buck-909090/*"
      ]
    }
  ]
}
```

* Click **Review**
* Name: **lambda-s3-policy**
* Click **Create policy**

---

# ✅ **5. Add S3 Trigger to Lambda**

* Go to **Lambda → Configuration → Triggers**
* Click **Add trigger**
* Select **S3**
* Bucket: **`lambda-test-buck-909090`**
* Event type: **All object create events**
* Click **Add**

---

# ✅ **6. Test Using Lambda Test Button (Optional)**

Go to **Test → Create test event**
Name: `s3-test-event`
Paste:

```json
{
  "Records": [
    {
      "eventVersion": "2.0",
      "eventSource": "aws:s3",
      "awsRegion": "us-east-1",  //region
      "eventTime": "1970-01-01T00:00:00.000Z",
      "eventName": "ObjectCreated:Put",
      "userIdentity": {
        "principalId": "EXAMPLE"
      },
      "requestParameters": {
        "sourceIPAddress": "127.0.0.1"
      },
      "responseElements": {
        "x-amz-request-id": "EXAMPLE123456789",
        "x-amz-id-2": "EXAMPLE123/5678abcdefghijklambdaisawesome/mnopqrstuvwxyzABCDEFGH"
      },
      "s3": {
        "s3SchemaVersion": "1.0",
        "configurationId": "testConfigRule",
        "bucket": {
          "name": "amzn-s3-demo-bucket",  //bucket name
          "ownerIdentity": {
            "principalId": "EXAMPLE"
          },
          "arn": "arn:aws:s3:::amzn-s3-demo-bucket" //bucker arn
        },
        "object": {
          "key": "test.png", //test file name
          "size": 1024,
          "eTag": "0123456789abcdef0123456789abcdef",
          "sequencer": "0A1B2C3D4E5F678901"
        }
      }
    }
  ]
}
```

Click **Test**

---

# ✅ **7. Real Test (Upload File to S3)**

* Go to **S3 → Open bucket → Upload**
* Upload any file (example: `test.txt`)

Lambda runs automatically.

---

# ✅ **8. Verify Logs in CloudWatch**

* Go to **CloudWatch Console**
* Click **Logs → Log groups**
* Open: **`/aws/lambda/s3-lambda-logger`**
* Open the latest **Log Stream**
* You will see:

```
Lambda Triggered!
{.... event details ....}
```

---

# 🎉 **Lab Completed Successfully!**

# AWS Lambda + S3 — Thumbnail Generator 

**Goal:** When an image is uploaded to a source S3 bucket, an AWS Lambda function resizes it and writes a thumbnail to a destination S3 bucket.
![AWS GIF](https://github.com/shyamdevk/AWS-Concepts-Labs/blob/images/resize.gif)

---

## Before you start (Prerequisites)

* An AWS account with permission to create S3, IAM, and Lambda resources.
* AWS CLI installed and configured (`aws configure`).
* Node.js (recommended) **or** Python installed locally if you want to build the deployment package.
* A terminal/command prompt (Linux/macOS: bash; Windows: WSL recommended).

---

## Lab overview (high level)

1. Create two S3 buckets: **source** (where you upload images) and **destination** (where thumbnails go).
2. Create an IAM policy and execution role for Lambda that allows S3 and CloudWatch Logs access.
3. Package the Lambda function (includes an image-resizing library such as `sharp` for Node.js or `Pillow` for Python).
4. Create the Lambda function and attach the role.
5. Configure the S3 trigger so that uploads to the source bucket invoke the Lambda.
6. Test by uploading an image and verifying the thumbnail appears in the destination bucket.
7. Clean up resources when done.

---

## 1) Create S3 buckets

**Console (quick):**

* Open Amazon S3 console → Create bucket → choose a unique bucket name (lowercase, numbers, hyphens, dots).
* Create two buckets in the same AWS Region — e.g. `my-lambda-source-bucket` and `my-lambda-dest-bucket`.

**CLI example:** (replace names & region)

```bash
aws s3api create-bucket --bucket my-lambda-source-bucket --region us-east-1 --create-bucket-configuration LocationConstraint=us-east-1
aws s3api create-bucket --bucket my-lambda-dest-bucket --region us-east-1 --create-bucket-configuration LocationConstraint=us-east-1
```

> Note: Keep both buckets in the same region as your Lambda function.

---

## 2) Upload a test image

**Console:** open source bucket → Upload → add a small JPG/PNG image → Upload.

**CLI:**

```bash
aws s3api put-object --bucket my-lambda-source-bucket --key test.jpg --body ./test.jpg
```

---

## 3) Create a permissions policy (JSON) for Lambda

Save this as `policy.json` (simple, limited to S3 & CloudWatch Logs):

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "logs:CreateLogGroup",
        "logs:CreateLogStream",
        "logs:PutLogEvents"
      ],
      "Resource": "arn:aws:logs:*:*:*"
    },
    {
      "Effect": "Allow",
      "Action": ["s3:GetObject"],
      "Resource": "arn:aws:s3:::*/*"
    },
    {
      "Effect": "Allow",
      "Action": ["s3:PutObject"],
      "Resource": "arn:aws:s3:::*/*"
    }
  ]
}
```

**CLI create policy:**

```bash
aws iam create-policy --policy-name LambdaS3Policy --policy-document file://policy.json
```

---

## 4) Create an execution role for Lambda

**Trust policy `trust-policy.json`:**

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {"Service": "lambda.amazonaws.com"},
      "Action": "sts:AssumeRole"
    }
  ]
}
```

**CLI commands:**

```bash
aws iam create-role --role-name LambdaS3Role --assume-role-policy-document file://trust-policy.json
# Attach the previously created policy (replace account id in arn if needed)
aws iam attach-role-policy --role-name LambdaS3Role --policy-arn arn:aws:iam::123456789012:policy/LambdaS3Policy
```

> If you created the policy in the same account, replace `123456789012` with your AWS account id (or read the policy ARN from the create-policy output).

---

## 5) Create the function deployment package

You can use **Node.js + sharp** (recommended for thumbnails) or **Python + Pillow**. Below are short samples.

### Node.js (using `sharp`)

1. Create a working directory and initialize npm:

```bash
mkdir lambda-s3 && cd lambda-s3
npm init -y
npm install @aws-sdk/client-s3 sharp
```

2. Create `index.mjs` (sample handler — replace `us-east-1` with your region):

```js
import { S3Client, GetObjectCommand, PutObjectCommand } from '@aws-sdk/client-s3';
import sharp from 'sharp';
import { Readable } from 'stream';

const s3 = new S3Client({ region: 'us-east-1' });

export const handler = async (event) => {
  const srcBucket = event.Records[0].s3.bucket.name;
  const srcKey = decodeURIComponent(event.Records[0].s3.object.key.replace(/\+/g, ' '));
  const dstBucket = srcBucket + '-resized'; // or set a fixed destination bucket name

  const getObjectResp = await s3.send(new GetObjectCommand({ Bucket: srcBucket, Key: srcKey }));
  const body = await streamToBuffer(getObjectResp.Body);

  const resized = await sharp(body).resize({ width: 200 }).toBuffer();

  await s3.send(new PutObjectCommand({ Bucket: dstBucket, Key: `thumb-${srcKey}`, Body: resized }));
  return { status: 'done' };
};

function streamToBuffer(stream) {
  return new Promise((resolve, reject) => {
    const chunks = [];
    stream.on('data', (c) => chunks.push(c));
    stream.on('end', () => resolve(Buffer.concat(chunks)));
    stream.on('error', reject);
  });
}
```

3. Zip the contents (`index.mjs` and `node_modules`) into `deployment.zip`:

```bash
zip -r deployment.zip index.mjs node_modules
```

### Python (using `Pillow`)

1. Create a folder, install Pillow into it, and add `lambda_function.py`.

`lambda_function.py` (simple):

```python
import boto3
from PIL import Image
import io

s3 = boto3.client('s3')

def lambda_handler(event, context):
    src_bucket = event['Records'][0]['s3']['bucket']['name']
    src_key = event['Records'][0]['s3']['object']['key']
    dst_bucket = src_bucket + '-resized'

    obj = s3.get_object(Bucket=src_bucket, Key=src_key)
    img = Image.open(obj['Body'])
    img.thumbnail((200, 200))

    out_buffer = io.BytesIO()
    img.save(out_buffer, format='JPEG')
    out_buffer.seek(0)

    s3.put_object(Bucket=dst_bucket, Key='thumb-' + src_key, Body=out_buffer, ContentType='image/jpeg')
    return {'statusCode': 200}
```

2. Create a deployment package (zip) that includes `lambda_function.py` and the `Pillow` library files.

> Packaging tip: For Python, install dependencies into a folder (`pip install Pillow -t .`) and zip the folder contents.

---

## 6) Create the Lambda function (Console or CLI)

**Console:** Lambda → Create function → Author from scratch → name `CreateThumbnail` → runtime (Node.js 18.x or Python 3.x) → Execution role: Use existing role `LambdaS3Role` → Create. Then upload the `deployment.zip` under Function code.

**CLI example (Node.js):**

```bash
aws lambda create-function \
  --function-name CreateThumbnail \
  --runtime nodejs18.x \
  --role arn:aws:iam::123456789012:role/LambdaS3Role \
  --handler index.handler \
  --zip-file fileb://deployment.zip
```

> Replace ARNs and filenames as needed.

---

## 7) Configure S3 to invoke Lambda (add trigger)

**Console:**

* Open source bucket → Properties → Event notifications → Create event notification.
* Set event types: `PUT` (All object create events).
* Destination: Lambda function → choose `CreateThumbnail` → Save.

**CLI:** you can use `aws s3api put-bucket-notification-configuration` (console is easier for beginners).

---

## 8) Test the Lambda function

1. Upload an image to the source bucket (console or `aws s3 cp`):

```bash
aws s3 cp ./test.jpg s3://my-lambda-source-bucket/test.jpg
```

2. Check the destination bucket for `thumb-test.jpg` or `thumb-<original>`.
3. If something fails, open CloudWatch Logs for the Lambda function to view the error traces.

**Optional:** In the Lambda console you can also run a dummy test event (use the S3 PUT event template) to run the function without uploading.

---

## 9) Clean up (avoid extra charges)

* Delete Lambda function
* Delete IAM role & policy
* Delete S3 buckets and objects

**CLI quick cleanup** (be careful):

```bash
aws lambda delete-function --function-name CreateThumbnail
aws iam detach-role-policy --role-name LambdaS3Role --policy-arn arn:aws:iam::123456789012:policy/LambdaS3Policy
aws iam delete-role --role-name LambdaS3Role
aws iam delete-policy --policy-arn arn:aws:iam::123456789012:policy/LambdaS3Policy
aws s3 rm s3://my-lambda-source-bucket --recursive
aws s3 rb s3://my-lambda-source-bucket
aws s3 rm s3://my-lambda-dest-bucket --recursive
aws s3 rb s3://my-lambda-dest-bucket
```

---

## Troubleshooting & tips

* **Permissions errors:** Check that the Lambda execution role has the `LambdaS3Policy` attached and that the role trust policy allows `lambda.amazonaws.com` to assume it.
* **Region mismatch:** Make sure S3 buckets and Lambda are in the same AWS Region.
* **Large dependencies:** `sharp` or `Pillow` can bloat the zip. For production, consider Lambda Layers to share heavy libraries.
* **Testing locally:** Use `aws lambda invoke` to run the function from CLI with a sample event JSON.
