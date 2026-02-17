## 🔐 AWS Identity and Access Management (IAM)

### 👤 IAM Limits
- **Users:** Up to `5000`
- **Groups:** Up to `300`
- **Managed Policies attached per entity:** Up to `10`

---

### 🧾 Types of Policies
1. **AWS Managed** — Managed by AWS  
2. **Customer Managed** — Created and managed by the user  

---

### 🆔 Amazon Resource Name (ARN)

**Example:**
```

arn:aws:iam::aws:policy/AmazonS3FullAccess

````

#### 🔹 Parts of the ARN:
| Part | Meaning |
|------|----------|
| `arn` | Amazon Resource Name |
| `aws` | AWS Partition (Standard Region) |
| `iam` | AWS Service (IAM) |
| `::` | No region (IAM is global) |
| `aws` | Owned by AWS (not your account) |
| `policy/AmazonS3FullAccess` | Specific policy name |

---

## 🧱 IAM Policies Example

### 🎯 Allow & Deny Access to Buckets
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowAccessToBuckets",
      "Effect": "Allow",
      "Action": "s3:*",
      "Resource": [
        "arn:aws:s3:::bucket-677",
        "arn:aws:s3:::test-bucket-7988"
      ]
    },
    {
      "Sid": "DenyRestrictedBucket",
      "Effect": "Deny",
      "Action": "s3:*",
      "Resource": [
        "arn:aws:s3:::test-bucket-86786"
      ]
    }
  ]
}
````

---

### 📄 Inline Policy

* Created for a **specific user** (not reusable across groups).

---

### 🧭 Types of IAM Policies

* Identity-based Policy
* Resource-based Policy
* Access Control List (ACL)
* Permission Boundaries
* Session Policy
* Organization Service Control Policy
* Organization Resource Control Policy

---

## 🧰 AWS Roles

### 🎯 Purpose

* Grant **temporary access** using **STS (Security Token Service)**.
* Used for **cross-account access** or **temporary credentials**.

### ⚙️ Steps to Create Role

1. Create a Role & Assign Permissions
2. Create Inline Policy for the User

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "Statement1",
      "Effect": "Allow",
      "Action": ["sts:AssumeRole"],
      "Resource": ["arn:aws:iam::526888234336:role/TempUse"]
    }
  ]
}
```

➡️ Then go to AWS Console → **Switch Role** under your profile.

---

## 🔄 Cross-Account Role Access

### 🧩 Goal

Allow your IAM user (Account A) to access your friend’s AWS Account (Account B).

---

### 👥 Friend’s Account (B)

1. Create Role → Choose **Another AWS Account**
2. Enter **Your Account ID**
3. Attach Policies (e.g., `AmazonS3ReadOnlyAccess`)
4. Copy Role ARN:

```
arn:aws:iam::123456789012:role/CrossAccountAccessRole
```

---

### 👤 Your Account (A)

Attach Policy to your IAM User:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "sts:AssumeRole",
      "Resource": "arn:aws:iam::123456789012:role/CrossAccountAccessRole"
    }
  ]
}
```

Then go to AWS Console → **Switch Role**

---

## 🖥️ EC2 — Launching Instance

1. Launch EC2 → Configure resources (CPU, memory, key pair)
2. Create IAM Role (e.g., to access S3)
3. Attach Role:
   `Actions → Security → Modify IAM Role`
4. Connect via terminal:

   ```bash
   ssh -i "C:\path\Demo-KeyServer.pem" ec2-user@<PublicIP>
   ```
5. Example command:

   ```bash
   aws s3 ls
   ```
6. Terminate after use → `Instance State → Terminate`

---

## 🧑‍💼 AWS Identity Center (SSO) vs AWS Organizations

| Feature  | AWS Identity Center            | AWS Organizations               |
| -------- | ------------------------------ | ------------------------------- |
| Purpose  | Central user access management | Manage multiple AWS accounts    |
| Function | Single Sign-On (SSO)           | Group accounts & set policies   |
| Control  | IAM Role Assignments           | Service Control Policies (SCPs) |
| Billing  | Per user                       | Consolidated Billing            |

---
