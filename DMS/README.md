# **📘 AWS DMS (Database Migration Service)**

![Screenshot](https://github.com/shyamdevk/AWS-Concepts-Labs/blob/images/dms.gif)

## 🟦 What is AWS DMS?

AWS DMS (Database Migration Service) is a fully managed service that helps you **migrate databases** from one environment to another **quickly and with very little downtime**.

It supports:

* On-premise ➝ AWS
* AWS ➝ AWS
* AWS ➝ On-premise
* One DB engine ➝ Another (Oracle ➝ MySQL)

---

## 🟩 Why Use AWS DMS?

* ✅ Easy & automated migration
* ✅ Minimal downtime
* ✅ Secure and reliable
* ✅ Supports many DB engines
* ✅ Continuous data replication
* ✅ Cost-effective

---

## ⚙️ How AWS DMS Works (Simple Flow)

1. **Create Replication Instance**
   The “middle server” that handles data movement.

2. **Configure Source Endpoint**
   Your existing/old database.

3. **Configure Target Endpoint**
   Your new database (AWS or external).

4. **Create a Migration Task**
   Choose how to move data: full load, CDC, etc.

5. **Start Migration**
   DMS copies data and continues syncing if needed.

---

## 🔁 Migration Task Types

| Type                | Meaning                              |
| ------------------- | ------------------------------------ |
| **Full Load**       | Moves existing data only             |
| **Full Load + CDC** | Moves existing data + future changes |
| **CDC Only**        | Only captures ongoing changes        |

---

## 🟧 Types of Migrations

### 🔹 Homogeneous Migration

Same database engine
**Example:** MySQL ➝ MySQL

### 🔹 Heterogeneous Migration

Different engines (use with AWS SCT)
**Example:** Oracle ➝ PostgreSQL

---

## 🔐 Security in AWS DMS

* TLS encryption **in transit**
* KMS encryption **at rest**
* IAM roles for access control
* VPC security groups for network protection

---

## 💡 Common Use Cases

* Move databases from **on-prem → AWS**
* Migrate to **Aurora, RDS, Redshift**
* Region-to-region DB migration
* Database engine change (Oracle → PostgreSQL)
* Real-time data replication using CDC
* Sync DB copies for analytics or backup

---

## ⭐ Key Benefits

* Minimal downtime ✔
* Easy setup ✔
* Supports major DB engines ✔
* Reliable replication ✔
* Pay only for the replication instance ✔

---
# 🚀 AWS DMS LAB — END-TO-END MIGRATION USING MYSQL (RDS ➝ RDS)

A clean, structured, beginner‑friendly **README.md** for your AWS Database Migration Service (DMS) Lab.

---

## 📌 **Lab Overview**

This lab walks you through migrating data from a **Source MySQL RDS** to a **Target MySQL RDS** using:

* 2 EC2 Instances (Source + Target)
* 2 Self-managed RDS instances
* AWS DMS (Replication Instance, Endpoints, Migration Task)

The goal is to verify successful data movement using DMS Full Load migration.

---

# 🏗️ **Lab Architecture (High Level)**

```
EC2-Source  →  Source RDS  →  AWS DMS  →  Target RDS  →  EC2-Destination
```

---

# ✅ **1. Prerequisites**

* AWS Account
* VPC + Subnets
* Security Groups configured for MySQL (port 3306)
* IAM permissions for RDS, EC2, DMS

---

# 🛡️ **2. Networking & Security Group Setup**

Create these Security Groups:

### **SG-Source-DB**

* Allow inbound **3306** from:

  * SG-Source-EC2
  * SG-DMS

### **SG-Target-DB**

* Allow inbound **3306** from:

  * SG-Target-EC2
  * SG-DMS

### **SG-DMS**

* Allow outbound **3306** to both RDS instances.

---

# 🗄️ **3. Create Two RDS Instances (MySQL)**

1. Create **Source RDS (MySQL)**
2. Create **Target RDS (MySQL)**
3. Attach the correct DB Security Groups
4. Note the RDS endpoints

---

# 💻 **4. Launch EC2 Instances (Source & Destination)**

### **Source EC2**

Install MySQL:

```bash
sudo yum update -y
sudo yum install -y mariadb-server mariadb
```

### **Destination EC2**

Install MySQL client:

```bash
sudo yum update -y
sudo yum install -y mariadb
```

---

# 🛠️ **5. Configure Source Database & Add Sample Data**

From **EC2‑Source**:

```bash
mysql -h <source-rds-endpoint> -u <username> -p
```

Inside MySQL:

```sql
CREATE DATABASE dms_demo;
USE dms_demo;

CREATE TABLE employees (
  id INT PRIMARY KEY AUTO_INCREMENT,
  name VARCHAR(100),
  role VARCHAR(100),
  salary DECIMAL(10,2)
);

INSERT INTO employees (name, role, salary) VALUES
('Alice','Engineer',70000.00),
('Bob','Analyst',60000.00),
('Charlie','Manager',90000.00);
```

Verify:

```sql
SELECT * FROM employees;
```

---

# 🔍 **6. Validate Target RDS Access**

From **EC2-Destination**:

```bash
mysql -h <target-rds-endpoint> -u <username> -p
SHOW DATABASES;
```

(Optional) Create target DB:

```sql
CREATE DATABASE dms_demo;
```

---

# 🔁 **7. Create DMS Replication Instance (Single AZ)**

Go to: **AWS Console → DMS → Replication Instances → Create**

* Instance class: `dms.t3.medium`
* Multi-AZ: **No**
* Subnet group: Choose appropriate subnets
* Security group: **SG-DMS**

Wait for **Available**.

---

# 🔗 **8. Create DMS Endpoints (Source & Target)**

### **Source Endpoint**

* Type: Source
* Engine: MySQL
* SSL: None
* Provide username/password

### **Target Endpoint**

* Type: Target
* Engine: MySQL
* SSL: None

### **Run Test Connection (Both)**

Must show **Successful**.

---

# 📦 **9. Create DMS Migration Task**

Go to: **DMS → Tasks → Create Task**

### **Task Configuration**

* Task type: **Full Load**
* Source: MySQL source endpoint
* Target: MySQL target endpoint

### **Table Mapping Rule**

Add selection rule:

```json
{
  "rules": [
    {
      "rule-type": "selection",
      "rule-id": "1",
      "rule-name": "include-database",
      "object-locator": {
        "schema-name": "dms_demo",
        "table-name": "%"
      },
      "rule-action": "include"
    }
  ]
}
```

Create Task → **Start Task**.

---

# 🧪 **10. Verify Migration on Target RDS**

From **EC2-Destination**:

```bash
mysql -h <target-rds-endpoint> -u <username> -p
USE dms_demo;
SELECT * FROM employees;
```

✔️ Data should match source.

---

# 🩹 **11. Troubleshooting Tips**

### Test fails?

* Check SG-DMS allowed outbound 3306
* DB user must have SELECT privileges
* RDS must be reachable from DMS subnet

MySQL DMS-required permissions:

```sql
GRANT SELECT, RELOAD, SHOW DATABASES, REPLICATION SLAVE, REPLICATION CLIENT ON *.* TO 'dmsuser'@'%';
FLUSH PRIVILEGES;
```

---

# 🧹 **12. Cleanup**

To avoid costs, delete:

* DMS Replication Instance
* DMS Endpoints
* EC2 Instances
* RDS Instances

---
Here is a **well-formatted, clean, decorated `README.md`** for **AWS Lambda (Basics)**:

---
