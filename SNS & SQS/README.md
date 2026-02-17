# 📢 Amazon SNS (Simple Notification Service) – Complete Guide & Lab Setup

![AWS GIF](https://github.com/shyamdevk/AWS-Concepts-Labs/blob/images/sns.gif)

Amazon SNS (Simple Notification Service) is a **fully managed messaging service** that enables applications to send notifications/messages to subscribers instantly and reliably.

---

## 🚀 Why Use Amazon SNS?

SNS is used to:

* ⚡ Send real-time alerts/notifications
* 🔄 Fan-out messages to multiple systems
* 🔗 Trigger workflows across AWS services
* 📩 Notify end-users via Email, SMS, Mobile Push

---

## 🔑 Key Concepts in SNS

### **1. 🧵 Topic**

A **Topic** is a communication channel where publishers send messages and subscribers receive them.

SNS supports two types of topics:

#### **1️⃣ Standard Topic**

* High throughput
* Best-effort ordering
* Possible duplicates
* Fastest delivery

#### **2️⃣ FIFO Topic**

* **F**irst **I**n **F**irst **O**ut ordering
* No duplicates
* Limited throughput
* Used when **message order matters**

---

### **2. 📨 Publisher**

* The **sender** of the message
* Can be an application or AWS service

  * e.g., CloudWatch, Lambda, EC2

---

### **3. 👤 Subscriber**

Receives messages from the topic.
Common subscriber types:

* 📧 Email
* 📱 SMS
* 🌐 HTTP/HTTPS endpoint
* 🔗 Amazon SQS
* 🧠 AWS Lambda

---

### **4. 💬 Message**

The actual notification/alert sent to subscribers.

---

## 🔄 SNS Workflow (Simple Flow)

```
Publisher → SNS Topic → Subscribers
```

**Steps:**

1. Create an SNS Topic
2. Add Subscriber(s)
3. Publisher sends a message
4. SNS delivers to all subscribers

---

## 💡 Common Use Cases

| Use Case                    | Example                   |
| --------------------------- | ------------------------- |
| Cloud alerts                | CloudWatch → SNS → Email  |
| Fan-out architecture        | SNS → multiple SQS queues |
| Serverless triggers         | SNS → Lambda              |
| User notifications          | Email/SMS alerts          |
| Microservices communication | Event-driven apps         |

---

## 📬 Types of SNS Messaging

### **1. Application-to-Application (A2A) Messaging**

Apps talk to apps using SNS.
 ![Screenshot](https://github.com/shyamdevk/AWS-Concepts-Labs/blob/images/app.png)

📌 Examples:

* SNS Topic → Lambda
* SNS → SQS
* CloudWatch → SNS → EC2/Lambda
* Microservice Event Distribution

SNS helps to **fan-out** messages to multiple backend systems.

---

### **2. Application-to-Person (A2P) Messaging**

Application sends messages directly to users.
 ![Screenshot](https://github.com/shyamdevk/AWS-Concepts-Labs/blob/images/app2.png)
📌 Examples:

* OTP
* Login Alert
* Bank Notifications
* Email Alerts

SNS supports:

* SMS
* Email
* Mobile push notifications

---

# 🧪 LAB: Send Notifications from S3 to User via SNS

Goal:
**Create an SNS Topic that sends a message to the user whenever an S3 Event occurs (like object upload/delete).**

---

## 🧷 Step-by-Step Instructions

### **1️⃣ Create an SNS Topic**

* Go to **SNS → Topics → Create Topic**
* Type: **Standard**
* Enter a name
* Click **Create Topic**

---

### **2️⃣ Create Subscription**

* SNS → Subscriptions → Create Subscription
* Choose the **Topic**
* Choose **Protocol** (Email recommended)
* Enter your email
* Check inbox → **Confirm Subscription**

---

### **3️⃣ Create an S3 Bucket**

* Go to **S3 → Create Bucket**
* Configure normally

---

### **4️⃣ Configure S3 Event Notification**

* Open your S3 bucket
* Go to **Properties → Event Notifications**
* Click **Create Event Notification**
* Enter:

  * Name
  * Event Type (e.g., “PUT”, “DELETE”)
  * Destination: **SNS Topic**
* Save

---

### **5️⃣ Test the Setup**

* Upload or delete any object in S3
* SNS will instantly send an email notification to your subscribed email

---

## ✔️ Result

🎉 You will receive an **email notification** whenever an S3 event (upload/delete) occurs!

This demonstrates how SNS integrates with S3 for event-driven messaging.

---

## 🛠️ Additional Tips (Optional Enhancements)

✔️ Use **SQS** instead of Email for backend processing
✔️ Trigger a **Lambda** function when an object is uploaded
✔️ Add **message filtering** to send specific types of alerts
✔️ Enable **SNS delivery status logging** for debugging

---

## 📘 Summary

Amazon SNS is a powerful service that supports both **application-to-application** and **application-to-person** communication. By integrating SNS with S3, you can create automated, event-driven alerting systems easily.

---

# **LAB 2 – AWS SNS & CloudWatch Alarm**

### **Send an SNS Email Notification When EC2 CPU Utilization Exceeds 50%**

This lab demonstrates how to configure **Amazon SNS** and **CloudWatch Alarms** to notify an end user when an **EC2 instance’s CPU Utilization goes above 50%**.

---

## ## 📌 **Overview**

You will learn how to:

1. Create an **SNS Topic**
2. Create an **SNS Subscription** (Email Notification)
3. Launch an **EC2 Instance with Detailed Monitoring**
4. Configure a **CloudWatch Alarm**
5. Automatically send an **Email alert** when CPU > 50%

---

# ## ✅ **Step 1: Create an SNS Topic**

1. Go to **AWS Console → SNS → Topics**
2. Click **Create Topic**
3. Choose **Standard** type

   * Standard = high throughput, immediate delivery
4. Enter a **Topic Name** (Example: `EC2-CPU-Alerts`)
5. Leave all defaults
6. Click **Create Topic**

This topic acts as the **channel** where CloudWatch will publish alerts.

---

# ## ✅ **Step 2: Create a Subscription (Email)**

1. Open the SNS Topic you created
2. Click **Create Subscription**
3. Select:

   * **Protocol:** Email
   * **Endpoint:** Your Email Address
4. Click **Create Subscription**

### ⚠️ Confirm Subscription

* You will receive an email from *AWS Notifications*
* Open it and click **Confirm Subscription**
* Status becomes **Confirmed** (not Pending)

This ensures SNS can send alerts to your email.

---

# ## ✅ **Step 3: Launch an EC2 Instance with Detailed Monitoring**

1. Go to **EC2 → Instances → Launch Instance**
2. Select any Amazon Linux/Ubuntu AMI
3. Choose instance type (t2.micro is fine)
4. Under **Monitoring**:

   * Enable **Detailed Monitoring** (1-minute metrics)
5. Configure storage, security group, key pair
6. Launch the instance

### Why enable Detailed Monitoring?

* CloudWatch basic monitoring = 5-minute intervals
* Detailed monitoring = 1-minute intervals
* Faster alarm triggers

---

# ## ✅ **Step 4: Create a CloudWatch Alarm for CPU Utilization**

1. Go to **CloudWatch → Alarms → Create Alarm**
2. Click **Select Metric**
3. Choose:

   * **EC2 → Per-Instance Metrics**
   * Select **CPUUtilization**
   * Choose your EC2 instance
4. Click **Select Metric**

### Configure the Alarm Condition

5. Set the **Threshold type**: Static
6. Set the threshold:

   * **Greater than 50%**
7. Set **Period** to 1 minute (works well with detailed monitoring)

### Configure Notification

8. In **Notification Settings:**

   * Choose: **In Alarm**
   * Select the **SNS Topic** you created earlier (EC2-CPU-Alerts)

This means whenever CPU > 50%, CloudWatch will publish an alert to SNS.

---

# ## ✅ **Step 5: Create and Test the Alarm**

1. Review the settings
2. Click **Create Alarm**

### Testing the Alarm

To trigger CPU > 50%, you can SSH into the EC2 instance and run:

```bash
sudo yum install stress -y
stress --cpu 2
```

After a short period:

* CPU rises above 50%
* Alarm enters **ALARM** state
* CloudWatch sends the event to SNS
* SNS sends an **email notification** to your inbox

---

# ## 🎉 **Result**

Whenever your EC2 instance’s CPU exceeds **50%**, you will receive a **real-time email alert** through AWS SNS.

This lab helps you understand:

* SNS Topics and subscriptions
* CloudWatch alarms
* Automated notifications
* Monitoring EC2 instances effectively

---
# 📘 **AWS SQS - Amazon Simple Queue Service**

![Screenshot](https://github.com/shyamdevk/AWS-Concepts-Labs/blob/images/sqs.gif)
---

## 🧭 **What is Amazon SQS?**

**Amazon Simple Queue Service (SQS)** is a **fully managed message queue** service that allows different parts of an application to communicate **asynchronously**.

➡️ **SQS = Temporary storage for messages until a worker processes them**

**Why use it?**

* To **decouple** microservices
* To **avoid system overload**
* To **ensure messages are never lost**

---

# 🟦 **Types of SQS Queues**

## 🔹 **1. Standard Queue (Default)**

* Supports **unlimited throughput**
* **At-least-once delivery**
* **Best-effort ordering** (not guaranteed)
* Suitable for high-scale systems
  ➡️ Use when *ordering is not important*.

## 🔹 **2. FIFO Queue (First-In-First-Out)**

* **Strict ordering** guaranteed
* **Exactly-once processing**
* Lower throughput than Standard
* Supports “**Message Group ID**” for parallel processing
  ➡️ Use when *order matters* (payments, transactions).

---

# 🚀 **How SQS Works (Simple Flow)**

1️⃣ **Producer sends message → SQS Queue**
2️⃣ SQS stores the message durably
3️⃣ **Consumer polls** the queue
4️⃣ Consumer processes the message
5️⃣ Consumer **deletes message** from queue

➡️ If not deleted, message becomes visible again after *visibility timeout*.

---

# 🔐 **Important Concepts**

### 📨 **Message**

* Max size: **256 KB**
* Can be JSON, text, XML, etc.

### ⏲️ **Visibility Timeout**

* Time during which a message stays **hidden** after being read.
* Prevents multiple workers from processing the same message.

### 📅 **Message Retention**

* How long SQS stores a message (1 min → 14 days).

### 🗳️ **Dead Letter Queue (DLQ)**

Failed messages (not processed after X retries) go into a **DLQ** for debugging.

### 🔁 **Long Polling**

* Waits until a message arrives (reduces empty responses).
* Cheaper & more efficient.

---

# 🛠️ **Features of SQS**

### ✔️ **Decoupling**

Connect services without direct communication.

### ✔️ **Highly Scalable**

Handles **millions of messages per second** automatically.

### ✔️ **Durable**

Messages stored across **multiple AZs**.

### ✔️ **Secure**

Supports:

* IAM policies
* KMS encryption
* Private access via VPC Endpoint

### ✔️ **Fully Managed**

No servers, no maintenance.

---

# 🎯 **When to Use SQS?**

* Microservices communication
* Order processing systems
* Video/image processing pipelines
* Logging & monitoring systems
* Asynchronous tasks
* Queue-based batch processing

---

# 🧰 **SQS Basic Commands (AWS CLI)**

```bash
# Create queue
aws sqs create-queue --queue-name MyQueue

# List queues
aws sqs list-queues

# Send message
aws sqs send-message --queue-url <URL> --message-body "Hello"

# Receive message
aws sqs receive-message --queue-url <URL>

# Delete message
aws sqs delete-message --queue-url <URL> --receipt-handle <handle>
```

---

# 📌 **SQS Pricing (Simple Overview)**

* First **1 million requests free/month**
* After that pay per request (very cheap)
* FIFO queues cost slightly more

---

# 🔄 **SNS vs SQS (Interview Notes)**

| Feature       | SNS                  | SQS                    |
| ------------- | -------------------- | ---------------------- |
| Type          | Pub/Sub              | Message Queue          |
| Delivers To   | Multiple subscribers | One consumer at a time |
| Message Order | No                   | FIFO (optional)        |
| Use Case      | Broadcast            | Decoupling workers     |

---

# 🧪 **Example Use Case (Simple)**

### 🛍️ **E-commerce Order Workflow**

1. User places an order
2. Backend sends order message → **SQS**
3. Worker reads message
4. Worker processes payment, inventory, email
5. Message deleted

➡️ System is scalable + reliable.

---

# 📝 **Final Summary**

* SQS = **Scalable message queue**
* Two types: **Standard** & **FIFO**
* Key elements: Visibility Timeout, DLQ, Long Polling
* Ideal for decoupled, async systems
* Cheap, secure, fault-tolerant

# 📦 AWS SQS – Simple Hands-On Lab

This lab teaches complete beginners how to use **Amazon Simple Queue Service (SQS)** using only the AWS Console.
You will **create a queue → send a message → receive it → delete it**.

---

## ✅ Prerequisites

* AWS account
* Access to AWS Console

---

## 🟩 Step 1 — Open SQS

1. Login to AWS Console
2. Search for **SQS**
3. Open **Simple Queue Service**

---

## 🟩 Step 2 — Create a Queue

1. Click **Create queue**
2. Select **Standard queue**
3. Enter queue name:

   ```
   my-demo-queue
   ```
4. Scroll down → Click **Create queue**

✔ The queue is created successfully.

---

## 🟩 Step 3 — Send a Message

1. Open your queue: **my-demo-queue**
2. Click **Send and receive messages**
3. In the **Message body**, type:

   ```
   Hello from SQS
   ```
4. Click **Send message**

✔ Message sent successfully.

---

## 🟩 Step 4 — Receive the Message

1. In the same window, click **Poll for messages**
2. Wait for the message to appear
3. Click the message to view its content

✔ You have received your message.

---

## 🟩 Step 5 — Delete the Message

1. Select the message
2. Click **Delete**
3. Confirm the deletion

✔ Your message has been removed from the queue.

---

## 🟩 Step 6 — Clean Up

To avoid keeping unused resources:

1. Go back to the SQS main page
2. Select **my-demo-queue**
3. Click **Delete queue**

✔ Queue deleted.

---

## 🎉 Lab Completed!

You successfully learned how to:

* Create an SQS queue
* Send a message
* Receive & view the message
* Delete the message
* Delete the queue
---

# 🌐 **AWS CORS – Cross-Origin Resource Sharing**
