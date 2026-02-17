# 🐳 **AWS ECS**
 ![Screenshot](https://github.com/shyamdevk/AWS-Concepts-Labs/blob/images/ecs1.gif)

A beginner-friendly, easy-to-understand explanation of **Containerization**, **Docker**, **AWS ECS**, and a **simple hands-on lab**.

---

# 📦 **1. Introduction to Containerization**

## 🔹 What is Containerization?

Containerization is the process of packaging an application along with all its dependencies (libraries, runtime, config files) into a lightweight, portable unit called a **container**.

### ⭐ Benefits of Containerization

* ⚡ Fast deployment
* 🧩 Works the same everywhere (no environment conflicts)
* 🔁 Easy scaling
* 🪶 Lightweight (uses less resources than VMs)
* 🚀 Portable across systems (laptop → server → cloud)

## 🐳 Docker (Most Popular Container Tool)

### 🧱 Docker Image

Blueprint of the application.

### 🟦 Docker Container

Running instance of the Docker image.

---

# 🟦 **2. What is AWS ECS (Elastic Container Service)?**
 ![Screenshot](https://github.com/shyamdevk/AWS-Concepts-Labs/blob/images/ecs.gif)

AWS ECS is a fully managed **container orchestration service** used to **run, manage, and scale Docker containers**.

Think of ECS as a service that takes care of:

* Scheduling containers
* Starting/stopping tasks
* Scaling automatically
* Load balancing your app
* Handling networking

---

# 🧱 **3. ECS Building Blocks (Simple Explanation)**

| Component           | Meaning                                                           |
| ------------------- | ----------------------------------------------------------------- |
| **Cluster**         | Group of resources running containers (EC2 or Fargate)            |
| **Task Definition** | Blueprint of how container should run (image, CPU, memory, ports) |
| **Task**            | A running container                                               |
| **Service**         | Ensures the required number of tasks are always running           |
| **ECR**             | AWS container registry to store Docker images                     |

---

# 🟩 **4. ECS Launch Types**

### 🔹 1. **EC2 Launch Type**

* Containers run on your EC2 instances
* You manage the servers
* More control, but more work

### 🔹 2. **Fargate Launch Type**

* Serverless containers
* No EC2 needed
* AWS manages everything
* Best for beginners

---

# 🌀 **5. ECS Workflow (Easy Diagram)**

```
Developer → Docker Image → Push to ECR
                ↓
             ECS Cluster
                ↓
        ECS Task Definition
                ↓
        ECS Service (Auto-scaling)
                ↓
            Running Tasks
                ↓
   ALB Load Balancer → Users
```

---

# 🟧 **6. Use Cases of ECS**

* Microservices
* Web applications
* APIs
* Background jobs
* Log processing systems
* CI/CD workloads

---

Got it!
Here is the **final README.md content**, fully formatted, clean, and ready for you to **copy & paste directly**.

---

# **AWS ECS — NGINX Lab (Using Amazon Linux CLI & ECR)**

A straightforward, fixed-version lab to **pull `nginx:latest` on an Amazon Linux host, tag it, push to ECR, and deploy on ECS (Fargate)**.
This version includes **all corrected commands** based on your errors.

---

## **Prerequisites**

* AWS account
* Amazon Linux EC2 (Amazon Linux 2023 or 2)
* Docker installed
* AWS CLI configured (or IAM role on EC2)
* ECR repository (we will create if not exists)

---

# **0 — Check OS Version (important)**

```bash
cat /etc/os-release
```

---

# **1 — Install Docker**

## **If Amazon Linux 2023:**

```bash
sudo dnf update -y
sudo dnf install docker -y
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker ec2-user
```

## **If Amazon Linux 2:**

```bash
sudo yum update -y
sudo amazon-linux-extras install docker -y
sudo service docker start
sudo usermod -aG docker ec2-user
```

Logout and login again.

Check Docker:

```bash
docker ps
```

---

# **2 — Configure AWS Credentials (if needed)**

If you saw this error earlier:

```
Unable to locate credentials
```

Run:

```bash
aws configure
```

Enter:

* Access Key ID
* Secret Key
* Region (us-east-1)
* Output: json

---

# **3 — Pull the NGINX Image (from Docker Hub)**

```bash
docker pull nginx:latest
```

Verify:

```bash
docker images
```

---

# **4 — Create an ECR Repository (if not exists)**

```bash
aws ecr describe-repositories --repository-names ecs-demo --region us-east-1 >/dev/null 2>&1 || \
aws ecr create-repository --repository-name ecs-demo --region us-east-1
```

---

# **5 — Login to ECR (Correct Syntax)**

```bash
aws ecr get-login-password --region us-east-1 \
| docker login --username AWS --password-stdin 526888234336.dkr.ecr.us-east-1.amazonaws.com
```

You should see:

```
Login Succeeded
```

---

# **6 — Tag the Local Image With ECR Repo URI**

```bash
docker tag nginx:latest 526888234336.dkr.ecr.us-east-1.amazonaws.com/ecs-demo:latest
```

Check tags:

```bash
docker images
```

---

# **7 — Push Image to ECR**

```bash
docker push 526888234336.dkr.ecr.us-east-1.amazonaws.com/ecs-demo:latest
```

---

# **8 — Create ECS Cluster (Fargate)**

AWS Console → ECS → Clusters → Create Cluster

* **Networking Only (Fargate)**
* Name: `ecs-nginx-cluster`

---

# **9 — Create ECS Task Definition**

Task Definitions → Create New

* **Launch Type:** Fargate
* **CPU:** 256
* **Memory:** 512
* **Add container**

Container Settings:

* Name: `nginx-container`
* Image:

  ```
  526888234336.dkr.ecr.us-east-1.amazonaws.com/ecs-demo:latest
  ```
* Port: **80**

Create.

---

# **10 — Create ECS Service**

Cluster → Create Service

* Launch Type: Fargate
* Service Name: `nginx-service`
* Tasks: 1

Networking:

* Select VPC
* Select **public subnets**
* Auto-assign public IP → **ENABLE**

Create service.

---

# **11 — Verify Deployment**

1. Go to ECS → Cluster → Tasks
2. Task status: **RUNNING**
3. Check **Public IP**
4. Open in browser:

```
http://<public-ip>
```

You should see the **NGINX Welcome Page** 🎉

---

# **Troubleshooting**

### **Error: unknown flag: --shyamdev**

Cause: Wrong docker login command
Fix: Always use

```bash
--username AWS --password-stdin
```

### **Error: An image does not exist locally**

Cause: You didn’t tag local image
Fix:

```bash
docker tag nginx:latest <ecr-uri>
```

### **Error: Unable to locate credentials**

Fix:

```bash
aws configure
```

### **Push denied**

IAM role/user missing permissions.

---

# **Final Flow Summary**

```
Amazon Linux → docker pull nginx → tag → ECR login → docker push → ECS (Fargate) → Public IP → NGINX page
```

---

# 🐳 AWS ECR (Elastic Container Registry) 
![Screenshot](https://github.com/shyamdevk/AWS-Concepts-Labs/blob/images/ecr.png)

## 📌 What is ECR?
**Amazon ECR (Elastic Container Registry)** is a fully managed service used to **store, manage, share, and deploy container images**.  
Think of it as a **private Docker Hub inside AWS**.

---

## 🎯 Why Do We Use ECR?
- To securely **store container images**.
- To integrate smoothly with **ECS, EKS, Lambda, and Fargate**.
- No need to manage registry servers.
- **Fast image pulls** inside AWS network.
- **IAM-based access control** for better security.
- **Supports versioning** and vulnerability scanning.

---

## ⭐ Key Features of AWS ECR
### 🔐 1. Secure & Private Repositories  
Images are encrypted and access is controlled using IAM.

### ⚙️ 2. Fully Managed  
AWS handles the infrastructure—no servers to maintain.

### 🚀 3. High Availability  
Replicated across multiple Availability Zones.

### 🔄 4. Image Versioning  
Multiple image tags and versions can be stored.

### 🛡️ 5. Vulnerability Scanning  
Detects security issues in images.

### 🧹 6. Lifecycle Policies  
Automatically delete unused/old images to save storage.

---

## ⚙️ How AWS ECR Works (Simple Steps – No Code)

1. **Create an ECR Repository** (example: `my-app`).
2. **Build your container image** on your local system.
3. **Authenticate** your local Docker client with ECR.
4. **Push the image** to the ECR repository.
5. AWS services (ECS, EKS, Lambda) **pull the image** automatically when running the application.
6. Manage **image tags**, run **scans**, and apply **lifecycle policies**.

---

## 🔄 Simple Workflow Diagram (Text-Based)

```

Developer → Build Docker Image
→ Tag Image
→ Push to ECR
→ ECS/EKS/Lambda Pulls Image
→ Application Runs on AWS

```

---

## 🧩 Visual Workflow Diagram (Reference)

> (Images shown are conceptual references)

```

    [ Developer ]
         |
         v
[ Docker Image Build ]
         |
         v
 [ Push Image to ECR ]
         |
         v
[ AWS ECS / EKS / Lambda Pulls Image ]
         |
         v
[ Application Runs ]

```
# 🐳 AWS ECR Lab – Push NGINX Image from EC2 & Pull Back

This lab teaches you how to:

- Pull **NGINX image** from Docker Hub  
- Configure **AWS CLI**  
- Push the image to **Amazon ECR**  
- Delete images from EC2  
- Pull the image **back from ECR**  
- (Optional) Run the container  

Everything is done using **EC2 CLI**.

---

## 📌 Prerequisites
- AWS EC2 instance (Amazon Linux or any Linux)
- Docker installed  
- AWS CLI installed  
- IAM user with ECR access  
- One ECR repository created  

---

# 1️⃣ Configure AWS CLI

Run on your EC2:

```bash
aws configure
````

Enter:

* **Access Key**
* **Secret Key**
* **Region** (ex: ap-south-1)
* **Output format** (json)

This allows EC2 to communicate with AWS services.

---

# 2️⃣ Create ECR Repository (Console Step)

Go to:

**AWS Console → ECR → Create Repository → Name: `nginx-demo`**

Copy the repository URI:

```
123456789012.dkr.ecr.ap-south-1.amazonaws.com/nginx-demo
```

ECR repository will store your Docker images.

---

# 3️⃣ Pull NGINX Image from Docker Hub

```bash
docker pull nginx
```

Check image:

```bash
docker images
```

This downloads the official nginx image to EC2.

---

# 4️⃣ Login to AWS ECR (Important)

Use the **View Push Commands** snippet from ECR:

```bash
aws ecr get-login-password --region ap-south-1 \
| docker login --username AWS --password-stdin 123456789012.dkr.ecr.ap-south-1.amazonaws.com
```

This authenticates Docker so it can push to ECR.

---

# 5️⃣ Tag the Image for ECR

```bash
docker tag nginx:latest 123456789012.dkr.ecr.ap-south-1.amazonaws.com/nginx-demo:latest
```

Tagging converts the local Docker image into an ECR-compatible format.

---

# 6️⃣ Push Image to ECR

```bash
docker push 123456789012.dkr.ecr.ap-south-1.amazonaws.com/nginx-demo:latest
```

The nginx image is now uploaded and stored in Amazon ECR.

---

# 7️⃣ Delete Image Locally from EC2

We remove both the DockerHub image and ECR-tagged image:

```bash
docker rmi nginx:latest
docker rmi 123456789012.dkr.ecr.ap-south-1.amazonaws.com/nginx-demo:latest
```

Verify deletion:

```bash
docker images
```

Now EC2 has **no nginx image**.

---

# 8️⃣ Pull Image Back from ECR to EC2

Login again (if needed):

```bash
aws ecr get-login-password --region ap-south-1 \
| docker login --username AWS --password-stdin 123456789012.dkr.ecr.ap-south-1.amazonaws.com
```

Pull the ECR image:

```bash
docker pull 123456789012.dkr.ecr.ap-south-1.amazonaws.com/nginx-demo:latest
```

Confirm:

```bash
docker images
```

The nginx image is restored to EC2 — but **from ECR** this time.

---

# 9️⃣ (Optional) Run the Container

```bash
docker run -d -p 80:80 nginx
```

Open EC2 Public IP → You should see the **NGINX welcome page**.

---

# 🔄 Workflow Summary Diagram (Simple)

```
Docker Hub → EC2 → Tag → Push to ECR → Delete Image → Pull from ECR → EC2
```

---

# ✅ Conclusion

This lab helps you understand:

* How ECR stores Docker images
* How to push and pull images from EC2
* How AWS CLI + Docker work together
* How images can be restored anytime from ECR

---
