## 🪣 Amazon S3 (Simple Storage Service)

![AWS GIF](https://github.com/shyamdevk/AWS-Concepts-Labs/blob/images/s3.gif)


### ⚙️ Features

* Unlimited storage
* Auto-scalable
* Highly reliable
* Max upload: **160 GB (Console)**, **5 TB (CLI)**
* Use **Multipart Upload** for >5TB files

---

### 🗂️ Storage Types

* **Block Storage** → EBS
* **File Storage** → EFS
* **Object Storage** → S3

---

### 🧾 Bucket Types

1. General Purpose
2. Directory Bucket (Low latency)
3. Table Bucket (Tabular data)

---

### 💻 CLI Commands

```bash
aws s3 ls                        # List Buckets
aws s3 mb s3://tempbuck0078      # Create Bucket
aws s3 rb s3://tempbuck0078 --force  # Delete Bucket
```

🔗 [More S3 CLI Commands](https://docs.aws.amazon.com/cli/latest/index.html)

---

## 🗃️ S3 Bucket Management

### 🧬 Versioning

* Prevent overwriting objects with the same name.
  **Types:**
* Versioned
* Unversioned
* Version Suspended

---

### 🌐 Access Object via URL

1. Enable ACLs
2. Disable “Block Public Access”
3. Make Object Public

---

## 🛡️ Object Lock

* Implements **WORM (Write Once, Read Many)** model.
* Prevents deletion/modification for a set period.

### 🕒 Retention Modes

* **Compliance Mode** — Even root can’t delete
* **Governance Mode** — Admins can modify with special permissions
* **Legal Hold** — No expiry until removed

---

## 🧩 S3 ACL (Access Control List)

| Type       | Purpose       | Permissions                      |
| ---------- | ------------- | -------------------------------- |
| Bucket ACL | Whole bucket  | READ, WRITE, READ_ACP, WRITE_ACP |
| Object ACL | Single object | READ, READ_ACP, WRITE_ACP        |

---

## 📜 Server Access Logging

* Logs every action in a bucket.
* To avoid recursive logs, store them in a **destination bucket**.

✅ Steps:

1. Create destination bucket
2. Enable logging in **source bucket (Properties tab)**
3. Check `.log` files in destination bucket

---

## 🔒 S3 Encryption

### 🧠 Types

| Type        | Where It Happens  | Key Managed By |
| ----------- | ----------------- | -------------- |
| Client-side | Before upload     | You            |
| SSE-S3      | In S3             | AWS            |
| SSE-KMS     | In S3             | KMS            |
| DSSE-KMS    | Dual KMS keys     | AWS KMS        |
| SSE-C       | Customer-provided | You            |

---

## 📄 Bucket Policy Example

Make all objects public:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::my-example-bucket/*"
    }
  ]
}
```

---

## 🔁 Replication

* **CRR (Cross-Region Replication):** Between regions
* **SRR (Same-Region Replication):** Within same region
* **Cross-Account Replication:** Between different accounts

---

## ⚙️ S3 Batch Operations

Perform bulk operations:

* Copy objects
* Modify tags or ACLs
* Trigger Lambda for each object

---

## 🏠 S3 Outposts

* Run S3 locally on AWS Outposts hardware.

---

## 🧮 Storage Classes & Lifecycle

### 🗂️ Frequent Access

* **S3 Standard**
* **S3 Express One Zone**
* **Reduced Redundancy (RRS)**

### 🕓 Infrequent Access

* **Standard-IA**
* **One Zone-IA**

### 📦 Archive

* **Glacier Instant Retrieval**
* **Glacier Flexible Retrieval**
* **Glacier Deep Archive**

### 🤖 Intelligent-Tiering

Automatically moves data between:

* Frequent Access
* Infrequent Access
* Archive Tiers

---

## ⏳ Lifecycle Configuration

**Two actions:**

* **Transition** → Move to cheaper storage
* **Expiration** → Delete after time period

✅ Steps:

1. Open bucket → **Management tab**
2. Create rule → Define transitions & expiration

---

## 🔔 S3 Event Notification

Triggers alerts for:

* Object created
* Object deleted
* Metadata changes

---

## ⚡ S3 Transfer Acceleration

* Uses CloudFront Edge locations for faster uploads/downloads.

---

## 💰 S3 Requester Pays

* Requester pays download & data transfer costs, not bucket owner.

---
