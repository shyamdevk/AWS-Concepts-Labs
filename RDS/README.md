
# **Amazon RDS (Relational Database Service)**

![AWS GIF](https://github.com/shyamdevk/AWS-Concepts-Labs/blob/images/rds.gif)

 ![Screenshot](https://github.com/shyamdevk/AWS-Concepts-Labs/blob/images/rds.png)

---

## ## 📌 **Overview**

**Amazon RDS** is a **fully managed relational database service** provided by AWS.
It simplifies the process of **setting up, operating, and scaling** databases in the cloud by handling the heavy lifting such as **backups, patching, monitoring, and high availability**.

AWS manages the infrastructure so developers can focus on **application logic**, not database administration.

---

# ## 🚀 **Why Use Amazon RDS? (Benefits)**

### **1️⃣ Fully Managed Service**

AWS automatically handles:

* Backups
* Patching
* Monitoring
* Hardware provisioning
* Failover

### **2️⃣ Automated Backups**

Supports **daily automatic backups** + **point-in-time recovery** to restore any moment within a retention period.

### **3️⃣ High Availability (Multi-AZ)**

* RDS automatically maintains a **standby replica** in another AZ.
* On failure, it performs **automatic failover**.
* Zero manual intervention.

### **4️⃣ Read Replicas**

* Used for **high read performance**.
* Offload read queries from the primary database.
* Available for MySQL, MariaDB, PostgreSQL, and Aurora.

### **5️⃣ Scalable**

* Scale **compute** (CPU/RAM) in minutes
* Scale **storage** (SSD/HDD) automatically or manually
* Support for **auto-scaling storage**

### **6️⃣ Secure by Design**

* Run inside **Amazon VPC**
* Encrypted using **KMS**
* Access control using **IAM** + **Security Groups**

---

# ## 🗂️ **Databases Supported by Amazon RDS**

| Engine                   | Description                                       |
| ------------------------ | ------------------------------------------------- |
| **MySQL**                | Open-source, widely used relational DB            |
| **PostgreSQL**           | Advanced open-source DB with rich features        |
| **MariaDB**              | MySQL-compatible community-driven DB              |
| **Oracle**               | Commercial enterprise database                    |
| **Microsoft SQL Server** | Popular enterprise database from Microsoft        |
| **Amazon Aurora**        | AWS-built, MySQL/PostgreSQL-compatible, 5× faster |

---

# ## ⚙️ **Key Features of AWS RDS**

### 🔹 **Automated Backups**

Enabled by default, supports point-in-time recovery.

### 🔹 **Manual Snapshots**

Your own custom DB snapshots that persist until deleted.

### 🔹 **Multi-AZ Deployment**

For high availability and automatic failover.

### 🔹 **Read Replicas**

Scale read traffic and improve performance.

### 🔹 **Monitoring & Metrics**

* CloudWatch metrics
* Enhanced Monitoring
* Performance Insights

### 🔹 **Automatic Software Patching**

Keeps the database engine up to date.

### 🔹 **Parameter Groups & Option Groups**

Customize DB configurations easily.

---

# ## 🎯 **When Should You Use RDS?**

Choose RDS when your application requires a **managed relational database system**, such as:

* Web and Mobile Applications
* E-commerce Platforms
* Banking & Financial Systems
* CRM / ERP Applications
* Data analytics and reporting
* Any application requiring ACID transactions

---

# ## 🎉 **Why RDS Compared to EC2 or On-Prem?**

RDS removes the burden of:

* OS installation
* Database installation
* Patching
* Backups
* Server maintenance
* High availability setup

AWS automates these tasks so you focus on **application development**.

---
# 📘 Exercise: Create a Database on EC2, Create a Table & Insert Data (MySQL)

This guide walks you through launching an EC2 instance, installing MySQL, creating a database, creating a table, and inserting sample data. A sample output screenshot is also included.

---

## 🚀 **Step 1: Launch an EC2 Instance**

* Choose **Ubuntu Server AMI** (24.04 or similar).
* Select instance type (t2.micro free tier).
* Configure storage & security group (allow SSH: port 22).
* Launch instance and SSH into it.

---

## 🛠️ **Step 2: Install MySQL Server on Ubuntu**

Update system:

```bash
sudo apt update
```

Install MySQL:

```bash
sudo apt install mysql-server
```

Start MySQL shell:

```bash
sudo mysql
```

---

## 🗄️ **Step 3: Create a Database**

View existing databases:

```sql
SHOW DATABASES;
```

Create new DB:

```sql
CREATE DATABASE mydb;
```

Use the new DB:

```sql
USE mydb;
```

---

## 🧱 **Step 4: Create a Table**

```sql
CREATE TABLE students (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(50),
    course VARCHAR(50)
);
```

---

## 📥 **Step 5: Insert Sample Data**

```sql
INSERT INTO students (name, course) VALUES
('Rahul', 'DevOps'),
('Ananya', 'Cybersecurity'),
('John', 'Cloud');
```

---

## 📤 **Step 6: View Table Contents**

```sql
SELECT * FROM students;
```

### ✔ Sample Output:

 ![Screenshot](https://github.com/shyamdevk/AWS-Concepts-Labs/blob/images/output.png)

---

## 🎉 Task Completed!

You have successfully:

* Launched an EC2 instance
* Installed MySQL
* Created a database
* Created a table
* Inserted multiple rows
* Retrieved data from the table


---

# 📘 **AWS RDS Components**

Amazon RDS (Relational Database Service) is a **managed database service** that helps you easily create, operate, and scale databases in the cloud.

This guide explains the **core components** of RDS in a clean and simple way.

---

## 🚀 **1. DB Instance**

A **DB Instance** is the actual **database server** Amazon RDS creates and manages for you.

### 🔹 What it includes:

* Database Engine
* Storage (data)
* CPU & RAM (instance class)

### 🔹 Think of it like:

A **virtual machine running your database**.

### 🔹 Example:

Creating a MySQL DB instance → AWS gives you a fully managed MySQL server.

---

## 🛠️ **2. DB Engine**

The **DB Engine** is the **database software** running on the RDS instance.

### 🔹 Supported Engines:

* MySQL
* PostgreSQL
* MariaDB
* Oracle
* SQL Server
* Amazon Aurora

### 🔹 Example:

Choose **MySQL Engine** → You can run MySQL queries, create tables, etc.

---

## ⚙️ **3. DB Instance Class**

The **DB Instance Class** defines the **hardware power** of your database instance.

### 🔹 It decides:

* CPU
* Memory (RAM)
* Network performance

### 🔹 Instance Class Examples:

| Instance Class | Power       | Use Case                  |
| -------------- | ----------- | ------------------------- |
| `db.t3.micro`  | Small       | Testing / Small apps      |
| `db.m5.large`  | Medium      | Production apps           |
| `db.r5.xlarge` | High Memory | Analytics / heavy queries |

### 🔹 Think of it like:

Choosing **the size and power of your computer**.

---

## 💽 **4. DB Instance Storage**

The storage used by your DB Instance to store:

* Data
* Indexes
* Logs

### 🔹 Storage Types:

| Storage Type                       | Description                   | Use Case        |
| ---------------------------------- | ----------------------------- | --------------- |
| **gp3 (General Purpose SSD)**      | Default, balanced performance | Most apps       |
| **io1/io2 (Provisioned IOPS SSD)** | High performance & IOPS       | Heavy workloads |
| Magnetic                           | Older type                    | Not recommended |

### 🔹 Additional Features:

* **Storage size**: e.g., 20GB, 100GB, 500GB
* **Storage autoscaling**: Automatically expands when needed

### 🔹 Example:

Setting **20GB gp3** = Your DB has a 20GB SSD disk.

---

## 🧩 **Quick Summary Table**

| Component             | Meaning                   | Simple Explanation  |
| --------------------- | ------------------------- | ------------------- |
| **DB Instance**       | The database server       | VM running your DB  |
| **DB Engine**         | The DB software           | MySQL? PostgreSQL?  |
| **DB Instance Class** | Hardware power            | CPU + RAM           |
| **DB Storage**        | Disk where DB stores data | SSD/HDD size & type |

---

## 🌟 **Extras (Useful Notes)**

### 🔐 Managed Security

* Supports **VPC**, **Security Groups**, **KMS encryption**, **IAM authentication**.

### 📈 Scalability

* Vertical scaling via instance class upgrade
* Storage auto-scaling for growing data

### 🛡️ Automated Backups

* Daily backups
* Point-in-time restore

### 💥 High Availability (Optional)

* Multi-AZ Deployment → Standby DB in another AZ

---

# 📘 **Lab Exercise: Create and Access a Database using Amazon Aurora & RDS**

This lab guides you through creating a **MySQL Database** using **Amazon Aurora / RDS** and connecting to it through an EC2 instance.

---

## 🏁 **Objective**

* Create a managed database using **Amazon Aurora / RDS**
* Launch an EC2 instance
* Install a MySQL/MariaDB client
* Connect to the database using the DB endpoint

---

# 🚀 **STEP 1: Create the Database (Aurora / RDS)**

Follow these steps in the AWS Console:

### 🔷 **1. Go to RDS → Databases → Create Database**

You will see two creation methods:

* **Standard create**
* **Easy create**

✅ Choose **Standard create**

---

### 🔷 **2. Choose the Engine**

* Select **MySQL** (or Aurora MySQL if needed)

---

### 🔷 **3. Choose Template**

* Select **Sandbox** (best for practice or free tier setups)

---

### 🔷 **4. Set Credentials**

* Master username → e.g., `admin`
* Master password → Choose a strong password
* Credential Management → **Self-managed** (you store the password)

---

### 🔷 **5. Configure Instance & Storage**

* Choose instance type (example):

  * `db.t3.micro` (free-tier eligible)
* Storage type:

  * GP3 or default SSD
* Enable storage autoscaling (optional)

---

### 🔷 **6. Connectivity**

* **Do NOT connect to EC2 automatically**
* Choose your VPC
* Choose or create a Security Group
* Public access → *No* (recommended)
* Authentication method → **Password authentication**

---

### 🔷 **7. Monitoring**

* Standard monitoring is fine
* Detailed monitoring → optional

---

### 🔷 **8. Create the Database**

Click **Create Database** and wait for the DB status to turn **Available**.

---

# 🖥️ **STEP 2: Access the Database from EC2**

Follow these steps to connect to your RDS / Aurora DB server.

---

## 🔷 **1. Launch an EC2 Instance**

* OS recommended: **Amazon Linux 2023** or **Amazon Linux 2**
* Attach it to the **same VPC** where RDS was created

---

## 🔷 **2. Install MySQL / MariaDB Client**

### 🟦 **If using Amazon Linux 2:**

```bash
sudo yum install mariadb105 -y
```

### 🟧 **If using Amazon Linux 2023 (AL2023):**

MySQL packages aren’t included by default → You must install via the MySQL repo:

```bash
sudo dnf install https://dev.mysql.com/get/mysql80-community-release-el9-1.noarch.rpm -y
sudo dnf clean all
sudo dnf makecache
sudo dnf install mysql-community-client -y
```

Verify:

```bash
mysql --version
```

---

## 🔷 **3. Edit RDS Security Group**

Allow inbound rule:

| Type         | Port | Source             |
| ------------ | ---- | ------------------ |
| MySQL/Aurora | 3306 | EC2 Security Group |

This allows EC2 to communicate with the DB.

---

## 🔷 **4. Connect to the Database**

Use the RDS **Endpoint** from the console.

### Syntax:

```bash
mysql -h <DB-ENDPOINT> -u <USERNAME> -p
```

### Example:

```bash
mysql -h database-1.cwfcqugwafxx.us-east-1.rds.amazonaws.com -u admin -p
```

After running the command, enter your DB password.

---

# 🎉 **You’re Now Connected!**

You can now run SQL commands such as:

```sql
SHOW DATABASES;
CREATE DATABASE testdb;
USE testdb;
```

---

# 📝 **Summary**

| Step  | What You Did                              |
| ----- | ----------------------------------------- |
| **1** | Created RDS/Aurora MySQL Database         |
| **2** | Configured credentials, storage & network |
| **3** | Launched EC2 and installed MySQL client   |
| **4** | Allowed EC2 → RDS connectivity            |
| **5** | Connected to DB using endpoint            |

---
