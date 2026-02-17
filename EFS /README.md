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

