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
