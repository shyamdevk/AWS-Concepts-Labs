# ☁️ Amazon EC2 — Elastic Compute Cloud

![AWS GIF](https://github.com/shyamdevk/AWS-Concepts-Labs/blob/images/ec2.gif)

> A complete and simplified guide to **Amazon EC2**, covering software installation, web hosting, AMIs, storage, snapshots, and instance types.

---

## 💡 What is Amazon EC2?

**Amazon EC2 (Elastic Compute Cloud)** is a web service that provides **scalable, on-demand virtual servers** — called **instances** — in the AWS Cloud.

It allows users to deploy applications quickly, scale easily, and pay only for the compute resources they use.

---

## 🧱 Installing Software on EC2

### 🔹 Exercise: Installing Apache HTTP Server

**Apache** is one of the most popular web servers used to host and deliver web content.

- Default web directory:  
```

/var/www/html

````

---

### ⚙️ Step-by-Step Lab

1. **Launch an EC2 Instance** → Connect to terminal.  
2. Access root privileges:
 ```bash
 sudo su
````

3. Install Apache:

   ```bash
   yum install httpd -y
   ```
4. Check Apache status:

   ```bash
   systemctl status httpd
   ```
   ![Screenshot](https://github.com/shyamdevk/AWS-Concepts-Labs/blob/images/dead.png)
5. If it’s inactive (dead), start the service:

   ```bash
   systemctl start httpd
   ```
   ![Screenshot](https://github.com/shyamdevk/AWS-Concepts-Labs/blob/images/run.png)
6. Copy your **Public IP** and paste it into a browser to test.

   * HTTP → Port **80**
   * HTTPS → Port **443**
7. If not accessible:

   * Edit **Security Group** → Add **HTTP (Port 80)** to **Inbound Rules**.
      ![Screenshot](https://github.com/shyamdevk/AWS-Concepts-Labs/blob/images/19.png)
8. To remove Apache:

   ```bash
   yum remove httpd -y
   ```

✅ Apache is now installed and running on your EC2 instance!

---

## 🌐 Host a Custom Website on EC2

1. **Connect** to EC2
2. **Install Apache**
3. Navigate to the web directory:

   ```bash
   cd /var/www/html
   ```
4. Create your webpage:

   ```bash
   sudo vi index.html
   ```

   Add your HTML code → Save (`:wq`)
5. Allow **Port 80** in your Security Group
6. Access:

   ```
   http://<EC2-Public-IP>
   ```
 ![Screenshot](https://github.com/shyamdevk/AWS-Concepts-Labs/blob/images/20.png)
 ![Screenshot](https://github.com/shyamdevk/AWS-Concepts-Labs/blob/images/21.png)
  
---

## 🎨 Import a Custom CSS Template to EC2

1. Download template:

   ```bash
   wget <link-to-css-template>
   ```

   > **wget** downloads files from the internet via HTTP/HTTPS/FTP.

2. Extract ZIP file:

   ```bash
   unzip <file.zip>
   ```

3. Move contents to web directory:

   ```bash
   mv * /var/www/html
   ```

4. Open your EC2 **Public IP** in a browser — the CSS-based website should load.

---

# ⚙️ Using NGINX (Engine-X)

**Nginx** is a high-performance web server, reverse proxy, and load balancer designed for scalability and speed.

* Default directory:

  ```
  /usr/share/nginx/html
  ```

---

### 🪜 Install and Run Nginx

1. Install Nginx:

   ```bash
   yum install nginx -y
   ```
2. Start the service:

   ```bash
   sudo systemctl start nginx
   ```
3. Test by visiting your **EC2 Public IP** in a browser.

---
 ![Screenshot](https://github.com/shyamdevk/AWS-Concepts-Labs/blob/images/22.png)

### 🧩 Add a Custom Website to Nginx

1. Navigate to:

   ```bash
   cd /usr/share/nginx/html
   ```
2. Remove default files:

   ```bash
   sudo rm -rf *
   ```
3. Create new site:

   ```bash
   sudo vi index.html
   ```
4. Add HTML → Save.

---

### 🖼️ Add a Custom Template to Nginx

1. Download template:

   ```bash
   wget <link-to-template.zip>
   ```
2. Extract files:

   ```bash
   sudo unzip <filename.zip>
   ```
3. Remove ZIP:

   ```bash
   sudo rm -rf <filename.zip>
   ```
4. Run your EC2 **Public IP** to test website display.

---

# 🐧 Software Installation in Ubuntu EC2

### 🧾 Basic Details

* **Username:** `ubuntu`
* **Package Manager:** `apt`

---

### 🪜 Install Apache

```bash
sudo apt install apache2 -y
cd /var/www/html
```

Also install unzip:

```bash
sudo apt install unzip -y
```

---

### 🪜 Install Nginx

1. Update system:

   ```bash
   sudo apt update
   ```
2. Install:

   ```bash
   sudo apt install nginx -y
   ```
3. Web directory path:

   ```
   /var/www/html
   ```

---

# 🔁 Proxy Concepts

### 🔹 Forward Proxy

* Acts **on behalf of clients**
* Filters and forwards client requests to the internet
* Used for **security**, **caching**, and **monitoring**

👉 **Flow:**

```
Client → Proxy → Internet
```

---

### 🔹 Reverse Proxy

* Acts **on behalf of servers**
* Balances load, handles SSL, and hides backend structure

👉 **Flow:**

```
Client → Reverse Proxy → Web Server
```

---

# 🪟 Connect EC2 (Windows AMI)

* **Protocol:** RDP (Remote Desktop Protocol)
* **Authentication:** Uses Key Pair for Password Decryption

---

### 🪜 Steps

1. Select your **Windows Instance** → **Connect**
2. Go to **RDP Client**
3. Upload **Key Pair**
4. Decrypt → Copy generated password
5. Open the downloaded **RDP file**
6. Paste decrypted password to connect.

---

## 🌍 Install IIS Web Server (Windows)

1. Open **Server Manager**
2. Go to **Add Roles and Features**
3. Choose **Role: IIS Web Server**
4. Wait for installation
5. Open browser → Enter EC2 **Public IP**
6. Default path:

   ```
   C:\inetpub\wwwroot
   ```
7. Use **Notepad** to create:

   ```
   index.html
   ```

---

# 🧩 AMI — Amazon Machine Image

### 📘 Definition

A template to launch EC2 instances with:

* OS
* Applications
* Configurations
* EBS Volumes

---

### 🧱 Types of AMIs

1. **AWS-provided**
2. **Marketplace**
3. **Custom/Existing**

✅ Used to launch **consistent environments** quickly.

---

## 🔹 1. Existing AMI

* Created from an **existing EC2 instance**
* Used for:

  * Cloning setup
  * Scaling
  * Rollbacks

---

## 🔹 2. Image Builder

AWS service to **automate AMI creation & updates**

**Features:**

* Automates patching & compliance
* Pipeline for testing and deployment

**Use Case:** Keep AMIs consistent across regions.

---

## 🔹 3. EBS Backup & Snapshots

* **EBS Volume:** Persistent storage for EC2
* **Snapshot:** Point-in-time backup

  * Incremental
  * Stored in **S3**

**Use Cases:**

* Data restoration
* AMI creation
* Migration

---

## 🔄 AMI Lifecycle
 ![Screenshot](https://github.com/shyamdevk/AWS-Concepts-Labs/blob/images/24.png)

1. Create EBS snapshot
2. Register as AMI
3. Launch new instances
4. Copy or share AMIs
5. Deregister when unused

> Command to auto-start Nginx:

```bash
systemctl enable nginx
```

---

## 🧠 Create Custom AMI

1. Select **Instance → Actions → Images and Templates → Create Image**
2. Configure name & settings
3. Check progress:

   ```
   EC2 → Images → AMIs
   ```
4. Launch a new instance from the AMI
5. Share via:

   ```
   AMI → Permissions → Configure ID/OUs
   ```

---

# 💾 Block Storage in AWS

Stores data in **fixed-size blocks**, offering **low-latency** performance for EC2.

---

## 📦 1. Instance Storage (Ephemeral)

* Temporary & attached to host
* High-speed
* Data lost on stop/terminate
* Use for cache/temp data

---

## 📦 2. Elastic Block Store (EBS)

* Persistent & network-attached
* Retains data after reboot
* Can attach/detach to multiple instances
* Supports **snapshots**

---

### ⚙️ Types of EBS Volumes

#### 🧠 SSD (Solid State Drives)

| Type        | Use Case                        |
| ----------- | ------------------------------- |
| **gp3/gp2** | General workloads, boot volumes |
| **io2/io1** | High IOPS, mission-critical DBs |

#### 💽 HDD (Hard Disk Drives)

| Type    | Use Case               |
| ------- | ---------------------- |
| **st1** | Big data, streaming    |
| **sc1** | Cold storage, low-cost |

---

# 🧪 Lab: Attach & Mount a New EBS Volume

### 🪜 Steps

1. EC2 → **Elastic Block Store → Volumes**
2. Create & configure volume
3. **Attach** to instance → Same region
4. Connect via terminal

Check devices:

```bash
lsblk
```

---

### 🧱 Mounting Process

1. Check filesystem:

   ```bash
   sudo file -s /dev/xvdb
   ```
2. Format disk:

   ```bash
   sudo mkfs -t ext4 /dev/xvdb
   ```
3. Mount volume:

   ```bash
   sudo mount /dev/xvdb /mnt/mountpoint
   ```
4. Verify mount:

   ```bash
   lsblk
   ```

---

### 🗂️ Permanent Mount

1. Remount after reboot:

   ```bash
   sudo mount /dev/xvdb /mnt/mountpoint
   ```
2. Edit `/etc/fstab`:

   ```
   /dev/xvdb /mnt/mountpoint ext4 defaults,nofail 0 2
   ```

✅ Mounts automatically on startup.

---

# 📸 EBS Snapshots

### 🔹 Definition

A **point-in-time backup** of an EBS volume — used for restore or replication.

**Features:**

* Incremental
* Stored in **S3**
* Cross-region support
* Automated backups via Lifecycle Policies

---

### 🧭 Snapshot Tutorial

1. Select **Volume → Actions → Create Snapshot**
2. Check **EC2 → Snapshots**
3. Create **Volume from Snapshot**
4. Mount it using:

   ```bash
   sudo mount -t xfs -o nouuid /dev/xvdb1 /mnt/mount
   ```
 ![Screenshot](https://github.com/shyamdevk/AWS-Concepts-Labs/blob/images/23.png)
> **UUID:** Universally Unique Identifier for disks/filesystems

---

# ⚙️ Amazon EC2 Instance Types

| Type                       | Example | Description                   |
| -------------------------- | ------- | ----------------------------- |
| **General Purpose**        | t3, m5  | Balanced CPU & memory         |
| **Compute Optimized**      | c5      | High CPU, compute-heavy tasks |
| **Memory Optimized**       | r5      | Large memory for databases    |
| **Storage Optimized**      | i3, d2  | High I/O performance          |
| **Accelerated Computing**  | p3, g4  | GPU-powered for AI/ML         |
| **High Performance (HPC)** | hpc6id  | Supercomputing workloads      |

---

## 🧩 EC2 Instance Naming Convention

Format:

```
<family><generation><features>.<size>
```

### Examples:

| Instance         | Meaning                              |
| ---------------- | ------------------------------------ |
| **t2.micro**     | General Purpose, 2nd Gen, small size |
| **c5.large**     | Compute Optimized, 5th Gen, mid-size |
| **r6g.xlarge**   | Memory Optimized, 6th Gen (Graviton) |
| **i3.2xlarge**   | Storage Optimized, 3rd Gen           |
| **g4dn.4xlarge** | GPU-based, 4th Gen with NVIDIA GPU   |

---
# ☁️ Amazon EC2 — Advanced Concepts

> A complete, practical guide to **Amazon EC2 advanced configurations**, covering purchasing models, SSH key management, automation, networking, and performance optimization.

---

## 💸 Amazon EC2 Purchasing Options

| Option | Description | Best For |
|--------|--------------|----------|
| **1️⃣ On-Demand Instances** | Pay only for what you use. No long-term commitment. | Short-term or unpredictable workloads. |
| **2️⃣ Reserved Instances (RI)** | Buy for 1 or 3 years at a discounted rate. | Long-term, steady workloads. |
| **3️⃣ Spot Instances** | Use unused EC2 capacity (up to 90% cheaper). Can be interrupted anytime. | Batch jobs, flexible & fault-tolerant tasks. |
| **4️⃣ Savings Plans** | Commit to a fixed $/hour usage for 1 or 3 years. Works across EC2, Fargate, Lambda. | Long-term workloads with flexibility. |
| **5️⃣ Dedicated Hosts** | Entire physical server dedicated to one customer. | Compliance, licensing, and isolation. |
| **6️⃣ Dedicated Instances** | Runs on isolated hardware (managed by AWS). | When isolation is needed without full control. |
| **7️⃣ Capacity Reservations** | Reserve capacity in a specific AZ. No long-term contract. | Ensures guaranteed instance availability. |

---

## 🔐 Key Pair & Recovery

A **Key Pair** in AWS consists of a **public key** and **private key** for secure SSH login.

- **Private Key:** Owned by the user.  
- **Public Key:** Stored inside the instance at  
```

/home/ec2-user/.ssh/authorized_keys

````

---

### 🧭 Exercise: Recover EC2 When Private Key is Lost (Using a Dummy Server)

1. **Detach** volume from the main instance.  
2. **Attach** it to a **dummy instance**.
3. **Mount** main volume:
 ```bash
 sudo mount -t xfs -o nouuid /dev/xvdb1 /mnt/mount
````

4. **Copy authorized_keys:**

   ```bash
   sudo mv authorized_keys /mnt/mount/home/ec2-user/.ssh/authorized_keys
   ```
5. **Detach** main volume → **Reattach** to the main instance (ensure device name = root).
6. Start instance → Connect using **dummy server’s key-pair**.

✅ The main instance now accepts the dummy server’s key-pair for SSH access.

---

### 🔑 Alternate Method: Generate a New RSA Key Pair

1. Create key pair:

   ```bash
   ssh-keygen -t rsa
   ```
2. Generated files inside `.ssh`:

   * `id_rsa` → Private Key
   * `id_rsa.pub` → Public Key
3. Move public key to `authorized_keys`:

   ```bash
   mv test.pub authorized_keys
   ```
4. Convert key to RSA (PEM) format:

   ```bash
   ssh-keygen -p -m PEM -f test
   ```
5. Copy public key → Save to text file → Use it for SSH authentication.

---

# 🐚 Linux Shell Scripting

### 🧠 Shell

A **shell** is a command-line interpreter that interacts with the OS.
**Examples:** Bash, Zsh, Sh, Ksh.

### ⚙️ Shell Scripting

Writing a series of shell commands in a `.sh` file to automate repetitive tasks.

---

### 🧪 Exercise: Create a Script to Install Apache & Host a Website

1. Create file:

   ```bash
   vi web.sh
   ```
2. Add:

   ```bash
   #!/bin/bash
   yum update -y
   yum install httpd -y
   systemctl start httpd
   systemctl enable httpd
   echo "<h1>Hi From Website</h1>" >> /var/www/html/index.html
   ```
3. Give execute permission:

   ```bash
   chmod 700 web.sh
   ```
4. Run script:

   ```bash
   ./web.sh
   ```

✅ Automatically installs Apache and hosts a simple web page.

---

# ⚡ User Data in EC2

### 🧠 Definition

**User Data** = A script or command that runs **automatically** when an EC2 instance first boots.

---

### 🪜 Steps

1. During EC2 creation → go to **Advanced Details → User Data**
2. Add:

   ```bash
   #!/bin/bash
   yum update -y
   yum install httpd -y
   systemctl start httpd
   systemctl enable httpd
   echo "<h1>Hi From Website</h1>" >> /var/www/html/index.html
   ```
3. After launch, Apache installs and runs automatically.

---

### 🧹 Updating User Data

* Stop the instance
* Go to **Actions → Edit User Data**
* To reload new data:

  ```bash
  sudo rm -rf /var/lib/cloud/*
  ```

  Then execute:

  ```bash
  sudo cloud-init init
  sudo cloud-init modules --mode=config
  sudo cloud-init modules --mode=final
  ```

✅ Clears cache and forces re-run of User Data.

---

# 🌐 Elastic Network Interface (ENI)

### 🧠 Definition

An **ENI** is a virtual network card attached to an EC2 instance.

Each ENI includes:

* Private IP (mandatory)
* Optional Public/Elastic IP
* MAC address
* Security Groups

---

### 🧭 Exercise: Attach an Additional ENI

1. **Instance → Networking → Network Interfaces**
2. Create new ENI → select **subnet** and **security group**
3. Actions → **Attach** to instance
4. Allocate **Elastic IP → Associate → Resource Type: Network Interface**
5. Confirm instance now has:

   * 2 Private IPs
   * 1 Public & Elastic IP

> 📝 Note: New ENIs don’t have a public IP unless manually assigned.

---

### 🎯 Assign Custom IP

During instance launch:

> Go to **Network Settings → Advanced Network Configuration → Custom Private IP**

---

# 🌍 Virtual Hosting

### 🧠 Definition

Host **multiple websites** on a **single EC2 instance** (or single IP address).

Two types:

1. **Name-Based Virtual Hosting** — by domain name
2. **IP-Based Virtual Hosting** — by IP address

---

### 🧭 Exercise: Host 2 Websites Using 2 ENIs

1. Inside `/var/www/html`, create two directories:

   ```
   web1
   web2
   ```
2. Add separate `index.html` files in each.
3. Edit config file:

   ```bash
   vi /etc/httpd/conf/httpd.conf
   ```

   Add:

   ```html
   <VirtualHost Private-IP1>
       DocumentRoot "/var/www/html/web1"
   </VirtualHost>

   <VirtualHost Private-IP2>
       DocumentRoot "/var/www/html/web2"
   </VirtualHost>
   ```
    ![Screenshot](https://github.com/shyamdevk/AWS-Concepts-Labs/blob/images/25.png)
4. Restart Apache:

   ```bash
   sudo systemctl restart httpd
   ```

✅ Access both web pages using the respective **Public or Elastic IPs**.

---

# 💻 PuTTY Software

**PuTTY** is a free SSH client for Windows that connects to remote systems via SSH, Telnet, or Rlogin.

### ⚙️ Setup Guide

1. Download **PuTTY** and **PuTTYgen** from the official site.
2. Convert `.pem` to `.ppk` using PuTTYgen.
3. In PuTTY:

   * **Session:** Enter Public IP
   * **SSH → Auth → Credentials:** Browse `.ppk` file
   * Click **Open**

✅ You’re connected to EC2 from Windows!

---

# 🔒 Session Manager (AWS Systems Manager)

**Session Manager** lets you connect to EC2 **without SSH, key pairs, or open ports**.

---

### ⚙️ Setup Steps

1. Ensure the **SSM Agent** is installed (preinstalled in most AMIs).
2. Create an IAM Role with:

   ```
   AmazonSSMManagedInstanceCore
   ```
3. Attach IAM role:

   ```
   Actions → Security → Modify IAM Role
   ```
4. Connect:

   ```
   Instance → Connect → Session Manager → Start Session
   ```

✅ Secure browser-based shell without SSH keys or open ports.

---

# 🖥️ EC2 Serial Console

### 🧠 Definition

Direct console access to EC2, similar to a **physical server monitor**.
Works **without the internet** (for troubleshooting system-level issues).

> Supported only on **Nitro-based instances**.

---

### ⚙️ Setup

1. Enable:

   ```
   EC2 → Settings → EC2 Serial Console → Allow
   ```
2. Stop instance → Restart on Nitro Hypervisor
3. Connect:

   ```
   Instance → Connect → EC2 Serial Console
   ```
4. Log in using **local user credentials** (create via CLI if not set).

---

# ⚙️ Burstable vs Fixed Performance Instances

### 💡 Classification Based on CPU Performance

#### 🔹 Burstable Performance Instances

* Provide **baseline CPU** performance with **credit-based bursting**.
* Earn CPU credits when idle.
* Example: `t2`, `t3`, `t3a`, `t4g`

Use for cost efficiency and variable workloads.

---

#### 🔹 Fixed Performance Instances

* Offer **consistent, dedicated CPU** performance.
* No CPU credits or bursts.
* Example: `m5`, `m6i`, `c5`, `r5`, `r6i`

Use for predictable workloads needing steady performance.

---

# ⚖️ Load Balancer in AWS

### 🧠 Definition

A **Load Balancer (ELB)** distributes incoming traffic across multiple targets (EC2s, containers, IPs).

---

### 🔹 How It Works

**Flow:**

```
User → Listener → Routing Rule → Target Group → EC2
```

**Components:**

* **Listener:** Detects requests (HTTP/HTTPS)
* **Routing:** Decides target group
* **Security Group:** Firewall for ELB
* **Health Checks:** Only routes to healthy targets

---

### 🎯 Purpose

* Improves availability & fault tolerance
* Increases scalability
* Routes traffic only to healthy instances

---

## 🧩 Types of Load Balancers

| Type                                   | Layer     | Use Case                                        |
| -------------------------------------- | --------- | ----------------------------------------------- |
| **1. Application Load Balancer (ALB)** | Layer 7   | Web apps, microservices (HTTP/HTTPS)            |
| **2. Network Load Balancer (NLB)**     | Layer 4   | TCP/UDP/TLS, real-time or low-latency apps      |
| **3. Gateway Load Balancer (GLB)**     | Layer 3   | Integrates with third-party firewalls & IDS/IPS |
| **4. Classic Load Balancer (CLB)**     | Layer 4/7 | Legacy load balancing (limited features)        |

---

# ⚖️ Load Balancer (Elastic Load Balancing - ELB)

A **Load Balancer (LB)** automatically distributes incoming network traffic across multiple EC2 instances to ensure high availability and reliability.

---

## 💡 LAB — Create Load Balancer in AWS

**Goal:** Create an **Application Load Balancer (ALB)** to manage traffic between multiple web servers.

### 🪜 Steps:

1. **Launch 2 EC2 Instances**
   - Deploy simple web pages on both (e.g., Apache with different content).

2. **Create a Target Group**
   - Go to: `EC2 → Target Groups → Create`
   - Select **Instances** as the target type.
   - Configure settings → Register both EC2 instances.
   - Create the **Target Group**.

3. **Create Load Balancer**
   - Navigate to: `EC2 → Load Balancers → Create Load Balancer`
   - Choose **Application Load Balancer**
   - Select **Internet-facing scheme**
   - Choose **subnets** and **security group**
   - Under **Listeners & Routing**, select your **Target Group**
   - Click **Create**

4. **Test Your Load Balancer**
   - Copy the **DNS name** of your ALB
   - Paste it in the browser:
     - It will load **one website**, and when refreshed, switch to the **other instance**.

✅ Ensure **HTTP (Port 80)** is allowed in the Load Balancer’s security group.

---

# ⚙️ Auto Scaling in AWS

### 🧠 Definition
**Auto Scaling** automatically adjusts the number of EC2 instances according to demand, ensuring consistent performance and cost optimization.

---

## 🔹 Types of Scaling

### 1️⃣ Horizontal Scaling (Scale Out/In)
- Adds or removes **instances** based on demand.  
  Example:
  - Traffic ↑ → Add EC2s  
  - Traffic ↓ → Remove EC2s

### 2️⃣ Vertical Scaling (Scale Up/Down)
- Changes **instance size** to increase/decrease performance.  
  Example:
  - Upgrade `t2.micro → t2.large`

---

# 🧩 Auto Scaling Group (ASG)

An **ASG** manages a group of EC2 instances collectively. It automatically maintains the desired instance count.

### ⚙️ Capacity Settings

| Setting | Description |
|----------|--------------|
| **Minimum Capacity** | Minimum number of instances always running |
| **Maximum Capacity** | Maximum limit of instances allowed |
| **Desired Capacity** | Normal (target) number of running instances |

---

# 🧱 Launch Template (LT)

A **Launch Template** defines configuration details for instances launched by Auto Scaling.

### 📦 Launch Template Includes:

- AMI (Amazon Machine Image)
- Instance Type (e.g., `t2.micro`)
- Key Pair (SSH access)
- Security Groups
- User Data (startup scripts)
- EBS Storage configuration
- IAM Role (permissions)

---

## 💡 LAB 1 — Create an Auto Scaling Group

### 🪜 Steps:

1. **Create a Launch Template**
   - `EC2 → Launch Templates → Create`
   - Add instance configuration (AMI, SG, Key Pair, etc.)

2. **Create Auto Scaling Group**
   - `EC2 → Auto Scaling Groups → Create`
   - Select Launch Template
   - Configure network/subnets
   - Define **Min, Max, Desired** capacity
   - Add notifications or tags (optional)
   - Create group

3. **Observe Activity**
   - View **Activity tab** to see scaling actions
   - If one instance stops, a new one is launched automatically.

4. **Manual Scaling**
   - `ASG → Edit → Capacity Overview`
   - Adjust instance count manually.

---

## 💡 LAB 1.1 — Integrate Load Balancer with Auto Scaling

1. Open ASG → **Integration tab**
2. Add or create a **Load Balancer**
3. Choose **Internet-Facing** scheme
4. Create/attach a **Target Group**
5. Allow **Port 80** (HTTP) in the security group.

✅ ASG now routes traffic through the Load Balancer and maintains availability.

---

# 🧾 Launch Configuration vs Launch Template

| Feature | Launch Configuration | Launch Template |
|----------|----------------------|-----------------|
| Versioning | ❌ Not supported | ✅ Supported |
| Reusability | Single-use | Multi-version |
| Network Interfaces | Limited | Full control |
| Security Group Updates | Manual recreation | Seamless version update |
| User Data | Basic | Advanced (scripts, parameters) |

---

## 🧪 Exercise — Modify Launch Template with Load Balancer

1. Create **Launch Template**
2. Launch **Load Balancer** using the template
3. Edit template:
   - `Actions → Modify → Create New Version`
4. Update version in **Load Balancer**
5. Relaunch instances → Apply new version settings.

---

# 📊 Auto Scaling Policies

**Scaling Policies** define *when and how* ASG adds/removes EC2 instances.

---

## 🔸 1. Dynamic Scaling Policy
- Based on **CloudWatch metrics** (e.g., CPU > 80%)
- Automatically adds/removes instances
![Screenshot](https://github.com/shyamdevk/AWS-Concepts-Labs/blob/images/27.png)
---

## 🔸 2. Predictive Scaling Policy
- Uses **machine learning** to forecast traffic
- Scales ahead of time
- Example: Adds instances every morning before peak hours

---

## 🔸 3. Scheduled Scaling Policy
- You manually set fixed times for scaling
- Example: Add 5 instances at 9 AM every weekday
 ![Screenshot](https://github.com/shyamdevk/AWS-Concepts-Labs/blob/images/28.png)
---

### ✅ Example Flow
```

CloudWatch monitors CPU → Policy triggers at 80% →
ASG adds new instances via Launch Template →
When CPU drops, extra instances are terminated

````

🧠 **Note:** Lab continues with CloudWatch integration.

---
