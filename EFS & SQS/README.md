# 📁 Amazon EFS (Elastic File System)

![AWS GIF](https://github.com/shyamdevk/AWS-Concepts-Labs/blob/images/efs.gif)

Amazon **EFS** is a **fully managed, scalable, shared file storage** service for **Linux-based applications**.  
It allows **multiple EC2 instances** to access the **same files at the same time** using the **NFS protocol**.

---

## 🚀 Why EFS?
EFS works like a **shared network folder**.  
If one EC2 writes a file, **all other EC2s can immediately see it**.  
You don’t need to manage storage size — EFS **grows and shrinks automatically**.

---

## ⭐ Key Features
- 🔄 **Auto-scaling** – Storage expands with your data  
- 🤝 **Shared access** – Many EC2 instances can mount it  
- 🛡️ **Highly Available** – Data stored across all AZs  
- 🔐 **Secure** – Supports encryption at rest & in transit  
- 💼 **Fully Managed** – No servers, no maintenance  
- 💰 **Pay-as-you-go** – Pay only for the space you use  

---

## 📘 How EFS Works (Simple)
1. You create an **EFS file system** in AWS.  
2. AWS automatically creates **mount targets** in each Availability Zone.  
3. EC2 instances connect to EFS using **NFSv4** protocol.  
4. All EC2 instances share the **same data** inside EFS.

---

## 🧰 Common Use Cases
- 🌐 Web servers (Apache/Nginx) sharing the same content  
- 📝 WordPress / CMS shared uploads folder  
- 📊 Big data & analytics processing  
- 🧑‍💻 Shared home directories for multiple users  
- 📦 ECS/EKS containers needing shared storage  

---

## 🔄 EFS vs EBS (Quick Comparison)
| Feature | **EFS** | **EBS** |
|--------|---------|---------|
| Type | File storage | Block storage |
| Access | Many EC2 instances | Single EC2 |
| Scaling | Automatic | Fixed size |
| Use Case | Shared data | OS disk, DB storage |

---

## 🛠️ Basic Mount Commands (Optional)
Install NFS tools:
```bash
sudo yum install -y nfs-utils   # Amazon Linux
sudo apt install -y nfs-common  # Ubuntu/Debian
````

Create mount directory:

```bash
sudo mkdir /efs
```

Mount EFS:

```bash
sudo mount -t nfs4 fs-xxxx.efs.<region>.amazonaws.com:/ /efs
```

---

## 🎯 Summary

Amazon EFS is best when you need:
✔ Shared storage
✔ Auto-scaling
✔ Multi-AZ durability
✔ Easy Linux + EC2 integration

Simple, scalable, and perfect for distributed applications.

---

# 🧪 LAB: Configure AWS EFS & Mount on Two EC2 Instances

This lab demonstrates how to **create an Amazon EFS file system**, mount it on **two EC2 instances**, and verify that **both instances can access the same shared storage**.

---

# 📌 **🎯 Objective**
- Create an **EFS File System**  
- Create **two EC2 instances**  
- Mount the **same EFS** on both instances (using different mount directories)  
- Verify **shared access** by creating a file from one instance and accessing it from the other

---

# 🏗️ Step 1 — Create EFS File System
1. Go to **EFS Console**
2. Click **Create File System**
3. Choose:  
   - ✅ *Recommended settings* (or customize if needed)  
4. Click **Create**
5. EFS file system will be created along with **Mount Targets** in each AZ.

---

# 🔐 Step 2 — Configure Security Group
To mount EFS using NFS:

### ✔ Ensure the EFS Security Group allows:
- **Inbound Rule:**  
  - *Type:* NFS  
  - *Port:* 2049  
  - *Source:* EC2 Instance Security Group

This ensures EC2 instances can connect to EFS.

---

# 💻 Step 3 — Launch Two EC2 Instances
1. Create **two Linux EC2 instances**
2. Attach them to:
   - Same VPC  
   - Same or reachable Subnets  
   - Security Groups that allow NFS communication  

---

# 📌 Step 4 — Mount EFS on EC2 Instance 1
1. Go to **EFS Console → Your EFS → Attach**
2. Choose **Mount via DNS**
3. Copy the NFS client command they provide

Example mount command:
```bash
sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport fs-06ecbb4def899ef60.efs.us-east-1.amazonaws.com:/ <directory-name>
````

### Create a mount directory:

```bash
sudo mkdir /efs1
```

### Mount EFS:

```bash
sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport fs-06ecbb4def899ef60.efs.us-east-1.amazonaws.com:/ /efs1
```

---

# 📌 Step 5 — Mount EFS on EC2 Instance 2

Repeat the same steps on the **second instance**.

### Create a different directory:

```bash
sudo mkdir /efs2
```

### Mount EFS:

```bash
sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport fs-06ecbb4def899ef60.efs.us-east-1.amazonaws.com:/ /efs2
```

---

# 📁 Step 6 — Test Shared Access (Important)

### On Instance 1:

```bash
cd /efs1
sudo touch testfile.txt
sudo mkdir testfolder
```

### On Instance 2:

```bash
cd /efs2
ls
```

### 🟢 You should see:

```
testfile.txt
testfolder
```

This confirms EFS is **mounted successfully** and **shared** between both EC2 instances.

---

# ✅ **Lab Completed!**

You have successfully:

* Created an EFS File System
* Mounted it on two EC2 instances
* Verified shared file access

This setup is commonly used in:

* Load-balanced web servers
* CMS applications (WordPress shared uploads)
* Any distributed system needing shared file storage

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
