# 🌐 **Amazon Route 53**

![AWS GIF](https://github.com/shyamdevk/AWS-Concepts-Labs/blob/images/route53.gif)

### ✅ **What is Route 53?**

Amazon **Route 53** is a **highly available, scalable, and global DNS (Domain Name System) service** by AWS.
It helps map **domain names → IP addresses** so users can reach websites and applications.

---

## ⭐ **Key Features**

### 🔹 **1. DNS Service**

* Converts domain name like `example.com` → IP address.
* Ensures users reach your application correctly.

### 🔹 **2. Domain Registration**

* You can **buy, manage, and transfer** domain names directly from Route 53.

### 🔹 **3. Health Checks**

* Route 53 monitors your application or server.
* If the main server fails, it automatically routes traffic to a **healthy backup**.

### 🔹 **4. Traffic Routing Policies**

Route 53 can route traffic intelligently using:

| Policy                    | Description                         |
| ------------------------- | ----------------------------------- |
| **Simple Routing**        | One record → one server             |
| **Weighted Routing**      | Split traffic based on weights      |
| **Latency-based Routing** | Sends user to lowest-latency region |
| **Failover Routing**      | Primary → Secondary backup          |
| **Geolocation Routing**   | Based on user’s geographic location |

---

## 📌 **Why the name "Route 53"?**

Because **DNS works on port 53**.

---

## 📝 **Short Note Summary**

**Route 53 = DNS + Domain Registration + Health Checks + Smart Traffic Routing.**

---

<br>

# 🌍 **DNS Resolution Explained**

### 🧠 **What is DNS?**

DNS (Domain Name System) translates domain names like `google.com` into IP addresses like `142.250.78.14`.

It’s basically the **“phonebook of the internet.”**

---

# 🔄 **DNS Resolution Flow (Step-by-Step)**

 ![Screenshot](https://github.com/shyamdevk/AWS-Concepts-Labs/blob/images/dns.png)

### **1️⃣ Browser → Checks Browser Cache**

* You type `www.example.com`
* Browser checks if the IP is already stored.

---

### **2️⃣ Browser → ISP DNS Resolver**

* If not cached, browser sends query to your **ISP’s DNS Resolver** (like Airtel/Jio resolver).

---

### **3️⃣ ISP DNS Resolver → Local Cache Check**

* Resolver checks its own cache.
* If no entry → goes to the **Root Server**.

---

### **4️⃣ ISP Resolver → Root Server**

* Root server doesn’t know exact IP.
* It tells which **TLD server** to ask.

Example:
`example.com` → go to **.com TLD server**

---

### **5️⃣ TLD Server → Authoritative Name Server**

* TLD server directs resolver to authoritative nameserver for that domain.

---

### **6️⃣ Authoritative Name Server → Returns IP**

* Contains the actual DNS records (A, AAAA, CNAME, MX).
* Responds with the real **IP address**.

---

### **7️⃣ Resolver → Caches Result**

* Stores the IP temporarily (TTL: Time To Live)
* Sends IP to browser

---

### **8️⃣ Browser → Connects to Web Server**

* Browser uses IP to load website.

---

# 📘 **Complete Notes Summary**

### **Amazon Route 53**

* Manages domains + DNS + traffic routing
* Highly available and global
* Supports advanced routing (failover, weighted, latency)

### **DNS Resolution**

* Browser → DNS Resolver → Root → TLD → Name Server → Browser
* Caching improves speed
* DNS makes internet human-friendly

---

# 📘 **AWS Route 53 DNS Mapping Lab (GoDaddy Domain → AWS ALB → EC2)**

This repository contains a detailed step-by-step lab guide for mapping a GoDaddy-purchased domain name to an AWS website hosted on an EC2 instance behind an Application Load Balancer (ALB).
Finally, HTTPS is enabled using AWS Certificate Manager (ACM).

---

## 🏗 **Architecture Overview**

```
GoDaddy Domain
      ↓ (Nameserver Update)
AWS Route 53 Hosted Zone
      ↓ (Alias Record)
Application Load Balancer (ALB)
      ↓
EC2 Instance
      ↓
Hosted Website
```

👉 *(Insert final architecture image here)*

---

## 🎯 **Lab Objectives**

* Import a GoDaddy-purchased domain into AWS Route 53
* Create a Public Hosted Zone
* Map the domain to an Application Load Balancer
* Validate domain ownership
* Issue an SSL certificate using AWS Certificate Manager
* Enable HTTPS on the Load Balancer
* Redirect HTTP → HTTPS

---

## 📝 **Prerequisites**

This lab assumes the following steps are already completed:

✔ EC2 instance created
✔ Website hosted and running
✔ Application Load Balancer created
✔ Domain name purchased from GoDaddy
✔ EC2 added to Target Group → ALB

---

# 🧪 **LAB STEPS**

---

# 🔹 **STEP 1: Create a Public Hosted Zone in Route 53**

1. Open **AWS Console → Route 53 → Hosted zones**
2. Click **Create hosted zone**
3. Enter your domain name (same as GoDaddy purchase)
4. Type: **Public hosted zone**
5. Click **Create**

Route 53 automatically creates:

* SOA record
* NS records (Name Servers)

📌 *You will copy these NS values to GoDaddy.*

---

# 🔹 **STEP 2: Update GoDaddy to Use AWS Name Servers**

1. Login to GoDaddy
2. Navigate to **My Products → Your Domain → Manage DNS**
3. Scroll to **Nameservers → Change**
4. Choose **Custom nameservers**
5. Enter the **4 AWS Route 53 nameservers**

Example:

```
ns-123.awsdns-45.com
ns-456.awsdns-78.org
ns-789.awsdns-12.net
ns-321.awsdns-90.co.uk
```

6. Save changes
7. Wait for DNS propagation (10–60 mins, sometimes 24 hrs)

📌 Your GoDaddy domain is now managed by Route 53.

---

# 🔹 **STEP 3: Get Your Load Balancer DNS Name**

1. Go to **AWS Console → EC2 → Load Balancers**
2. Select your Application Load Balancer
3. Copy the **DNS Name**, for example:

```
myapp-alb-1234567.ap-south-1.elb.amazonaws.com
```

You will map your domain to this ALB.

---

# 🔹 **STEP 4: Create DNS Records in Route 53**

### ✅ **Record 1: A Record (Alias → ALB)**

**Purpose:** Map `yourdomain.com` to ALB.

1. Open **Route 53 → Hosted zone**
2. Click **Create record**
3. Settings:

| Field        | Value           |
| ------------ | --------------- |
| Record Name  | *(leave empty)* |
| Type         | A               |
| Alias        | Yes             |
| Alias Target | Choose your ALB |

4. Click **Create Record**

This creates:

```
yourdomain.com → ALB → EC2 → Website
```

---

### ✅ **Record 2: CNAME (www → root domain)**

**Purpose:** Allow users to access [www.yourdomain.com](http://www.yourdomain.com).

1. Create a new record
2. Type: **CNAME**
3. Name: `www`
4. Value:

```
yourdomain.com
```

5. Save

---

# 🔹 **STEP 5: Request an SSL Certificate in ACM**

### 1. Open **AWS Certificate Manager (ACM)**

Make sure to choose the **same region as ALB**.

### 2. Click **Request Certificate → Request a Public Certificate**

Add:

```
yourdomain.com
www.yourdomain.com
```

Click **Next**

### 3. Validation Method:

✔ **DNS Validation**

Click **Review → Request**

### 4. Add DNS Validation Records

ACM shows CNAME validation records.

Click:

```
Create records in Route 53
```

Route 53 automatically adds the validation DNS records.

Status changes:

```
Pending validation → Issued
```

---

# 🔹 **STEP 6: Attach SSL Certificate to the Load Balancer**

1. Go to **EC2 → Load Balancers**
2. Select ALB
3. Open **Listeners** tab
4. Click **Add Listener**
5. Configure:

| Setting        | Value                   |
| -------------- | ----------------------- |
| Protocol       | HTTPS                   |
| Port           | 443                     |
| Certificate    | Choose ACM certificate  |
| Default Action | Forward to Target Group |

6. Save

🔒 Your site now supports HTTPS.

---

# 🔹 **STEP 7: Redirect HTTP → HTTPS (Optional but Recommended)**

1. Select **HTTP (80)** listener
2. Click **Edit**
3. Change action to:

```
Redirect to → HTTPS:443
```

4. Save

Now:

```
http://yourdomain.com → https://yourdomain.com
```

---

# 🔹 **STEP 8: Test the Website**

Open:

* ✔ [https://yourdomain.com](https://yourdomain.com)
* ✔ [https://www.yourdomain.com](https://www.yourdomain.com)

Check:

* SSL padlock visible
* Correct website loads
* Redirect works

---

# 🧼 **Cleanup (Optional)**

* Delete Route 53 Hosted Zone
* Delete ACM certificate
* Remove DNS records
* Delete ALB
* Terminate EC2 instance

---

# 📂 **Repository Structure Suggestion**

```
📁 aws-route53-dns-mapping-lab
│── README.md
│── images/
│     └── architecture-diagram.png
│── steps/
│     └── route53-setup.txt
│     └── acm-https.txt
│     └── dns-records.txt
```

---

# 📄 **Conclusion**

You successfully:

* Imported a GoDaddy domain into Route 53
* Mapped it to an AWS Application Load Balancer
* Configured DNS records
* Validated domain ownership
* Enabled HTTPS using ACM
* Redirected all traffic to secure HTTPS

Your domain now loads your EC2-hosted website securely through the ALB.

---

# 🌐 **AWS CloudFront**

![AWS GIF](https://github.com/shyamdevk/AWS-Concepts-Labs/blob/images/cloudfront.gif)

> A simple, clean and beginner-friendly explanation of AWS CloudFront.
> Suitable for notes, seminars, and GitHub documentation.

---

## 🚀 **What is AWS CloudFront?**

AWS CloudFront is a **Content Delivery Network (CDN)** service that delivers web content like images, videos, HTML, CSS, JS, and APIs **faster** using a global network of **Edge Locations**.

👉 **CloudFront = Global CDN + Caching + Fast Delivery + Security**

---

## 🗺️ **CloudFront Architecture (Diagram Placeholder)**

*Add your image here*

```
📌 Place CloudFront architecture diagram here
```

---

## ⭐ **Why Use CloudFront? (Advantages)**

### ✔ **1. Faster Content Delivery**

Delivers content from the nearest **Edge Location**, reducing latency.

### ✔ **2. Caching Improves Performance**

Caches images, videos, static files → reduces origin server load.

### ✔ **3. High Security**

* HTTPS support
* AWS Shield (DDoS protection)
* AWS WAF integration
* Signed URLs / Cookies

### ✔ **4. Very Cost-Effective**

Less data transferred from S3/EC2 → lower AWS bill.

### ✔ **5. Scalable & Reliable**

Handles millions of requests globally without manual scaling.

---

## 🔧 **How CloudFront Works (Simple Flow)**

*Add your flow diagram here*

```
User → Edge Location → Cache Hit? → If No → Origin (S3/EC2/ALB) → Cached → User
```

### 📌 Steps:

1. User requests a file (HTML, image, video, API).
2. Request goes to nearest **CloudFront Edge Location**.
3. If cached → returned instantly (**cache hit**).
4. If not cached → CloudFront fetches from **Origin**.
5. Caches it → sends to the user.

---

## 🧩 **CloudFront Components**

### 🏢 **1. Distribution**

The main configuration for CloudFront.

### 📦 **2. Origin**

The real backend:

* Amazon **S3**
* **EC2**
* **Application Load Balancer**
* Custom server

### 🌍 **3. Edge Locations**

Global data centers for caching content.

### ⚙ **4. Cache Behavior**

Controls:

* Which files to cache
* Allowed HTTP methods
* HTTP vs HTTPS
* TTL (cache time)

---

## 🔐 **Security Features**

| Feature                         | Purpose                                    |
| ------------------------------- | ------------------------------------------ |
| **HTTPS / TLS**                 | Encrypts data                              |
| **AWS WAF**                     | Blocks attacks (SQLi, XSS, bots)           |
| **AWS Shield**                  | DDoS protection                            |
| **Signed URLs**                 | Restrict access to premium/private content |
| **Origin Access Control (OAC)** | Secure private S3 buckets                  |

---

## 🎯 **Use Cases of CloudFront**

| Use Case             | Example                     |
| -------------------- | --------------------------- |
| Website Acceleration | Faster global websites      |
| Video Streaming      | OTT platforms               |
| Image Delivery       | E-commerce product images   |
| API Caching          | Faster API calls            |
| Secure Delivery      | Paid content, private files |

---

## 📝 **Quick Summary**

* CloudFront is AWS’s **CDN** service.
* Delivers content quickly using **edge locations**.
* Supports caching, security, HTTPS, WAF, and signed URLs.
* Works best with **S3**, **EC2**, **ALB**, **API Gateway**.
* Very useful for global websites, APIs, images, videos.

---
# 🌐 **AWS CloudFront — Hands-On Lab**

> This lab helps you understand how to deliver content using **AWS CloudFront** with an **Application Load Balancer (ALB)** hosting a sample website on EC2.

---

## 📘 **Lab Overview**

In this lab you will:

1. Launch an EC2 instance with a sample webpage
2. Create a Load Balancer for the EC2 instance
3. Create a CloudFront Distribution
4. Connect CloudFront to the Load Balancer as the Origin
5. Access your website using the CloudFront global CDN URL

---

# 🏁 **Step 1 — Launch EC2 Instance With Sample Webpage**

Use the following **User Data** script when launching EC2:

```bash
#!/bin/bash
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd

echo "<h1>Welcome to My EC2 Website</h1>" > /var/www/html/index.html
echo "<p>This website is served through an Application Load Balancer.</p>" >> /var/www/html/index.html
```

✔ This creates a simple webpage
✔ Apache starts automatically

---

# 🏗️ **Step 2 — Create an Application Load Balancer (ALB)**

### Follow these steps:

1. Open **EC2 Console → Load Balancers**
2. Click **Create Load Balancer → Application Load Balancer**
3. Set:

   * **Scheme:** Internet-facing
   * **IP type:** IPv4
4. **Listeners:** HTTP (port 80)
5. **Availability Zones:** Select at least 2
6. **Target Group:**

   * Create a new target group
   * Register your EC2 instance

After creation, open the ALB **DNS name** in your browser to verify the website loads.

---

# 🌍 **Step 3 — Create CloudFront Distribution**

### Steps:

1. Open **CloudFront Console**
2. Click **Create Distribution**
3. In **Origin Domain**, choose your **Load Balancer**
4. Keep **viewer protocol policy** as:

   ```
   Redirect HTTP to HTTPS  (recommended)
   ```

   If your ALB does not support HTTPS yet → you can change this later.

---

# 🎛️ **Step 4 — (Optional Fix) Edit Origin Settings**

If your Load Balancer only supports **HTTP**, CloudFront HTTPS may fail.

To fix this:

1. Open the Distribution
2. Go to **Origins → Edit**
3. Change:

   * **Origin Protocol Policy:** HTTP Only
4. Save changes
5. CloudFront will deploy the update (takes 3–5 minutes)

---

# 🔗 **Step 5 — Access Your Website Through CloudFront**

After the distribution status shows **Enabled**, copy the CloudFront domain:

```
https://d123example.cloudfront.net
```

Paste it in your browser → your EC2 website will load from **global edge locations**.

🎉 Congratulations!
You are now serving your website using **AWS CloudFront CDN + Load Balancer + EC2**.

---

# 📝 **Summary**

| Component  | Purpose                              |
| ---------- | ------------------------------------ |
| EC2        | Hosts your sample webpage            |
| ALB        | Distributes traffic across instances |
| CloudFront | Serves content fast using global CDN |

CloudFront improves performance, caching, scalability, and global delivery.

---
# 🌐 **LAB: Host Static Website on S3 + Deliver via CloudFront (With OAC)**

> This lab teaches you how to host a static website on an **S3 bucket** and deliver it globally using **AWS CloudFront** with **Origin Access Control (OAC)**.
>
> Also includes the fix for **CSS, JS, images not loading (403 AccessDenied)** by applying the correct S3 bucket policy.

---

## 📁 **Folder Structure Example**

```
index.html
assets/
   ├── css/
   └── js/
images/
   ├── logo.png
   └── banner.jpg
```

---

# 🧪 **STEPS**

---

# ✅ **Step 1 — Create an S3 Bucket**

1. Open **AWS → S3**
2. Click **Create bucket**
3. Bucket name: `buckerr989898`
4. Leave **Block Public Access ON** (required for OAC)
5. Create bucket

---

# ✅ **Step 2 — Upload Website Files**

Upload:

* `index.html`
* `assets/` folder
* `images/` folder

---

# ✅ **Step 3 — Enable Static Website Hosting**

Inside **Bucket → Properties**:

1. Enable **Static website hosting**
2. Set:

   * Index document: `index.html`
   * Error document: `index.html` (optional for SPA)

---

# ✅ **Step 4 — Create CloudFront Distribution**

1. Open **CloudFront** → **Create Distribution**
2. Origin domain → Choose the S3 bucket
3. **Origin Access Control (OAC)** → *Enable (recommended)*
4. Create distribution

---

# ✅ **Step 5 — Apply Correct S3 Bucket Policy (Fix CSS/JS/Images 403 Error)**

This policy allows **CloudFront** to load:

✔ index.html
✔ asset files
✔ image files
✔ CSS / JS / PNG / JPG / SVG

### ✔ **Final Working Bucket Policy (OAC)**

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowCloudFrontOACAccess",
      "Effect": "Allow",
      "Principal": {
        "Service": "cloudfront.amazonaws.com"
      },
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::buckerr989898/*",
      "Condition": {
        "StringEquals": {
          "AWS:SourceArn": "arn:aws:cloudfront::526888234336:distribution/EBEJZQVVTMTHA"
        }
      }
    }
  ]
}
```

👉 **This makes everything under the bucket public to CloudFront**, including:

* `assets/css/font-awesome.min.css`
* `assets/js/*`
* `images/*`

No need to change any additional permissions.

---

# ✅ **Step 6 — Invalidate CloudFront Cache**

After updating the policy:

1. CloudFront → **Invalidations**
2. Create **new invalidation**
3. Enter:

```
/*
```

This forces CloudFront to fetch new files.

---

# ✅ **Step 7 — Test the Website**

Open the CloudFront domain:

```
https://dxxxxxx.cloudfront.net
```

Now your:

✔ CSS loads
✔ JS loads
✔ Images load
✔ Full website works 🎉

---

# 📌 **Common Issue: Only index.html Loads**

This happens when:

❌ assets/ or images/ folders are private
❌ wrong bucket policy
❌ CloudFront not authorized to read objects

The above **OAC bucket policy fixes this 100%**.

---

# 🎉 **Lab Completed Successfully**

You have now hosted a static website on **S3** and delivered it via **CloudFront** with correct permissions.

# 🌐 **LAB: Give CloudFront Access to a Private S3 Bucket & Host Website**

This lab explains **how to host a static website in a PRIVATE S3 bucket** and **allow CloudFront to access it securely using OAC (Origin Access Control)**.

---

## 🧪 **LAB NAME:**

**Host Website on Private S3 Bucket Using CloudFront (OAC)**

---

# ✅ **STEPS**

## **1️⃣ Create an S3 Bucket (Private)**

1. Go to **AWS S3 Console → Create bucket**
2. Bucket name example:

   ```
   buckerr989898
   ```
3. Keep **Block Public Access = ON** (required for private hosting)
4. Upload your files:

   * `index.html`
   * `assets/` folder (CSS/JS)
   * `images/` folder

---

## **2️⃣ Create CloudFront Distribution**

1. Go to **CloudFront Console**
2. Click **Create Distribution**
3. **Origin domain:** Choose your S3 bucket
4. Under "Origin access":

   * Select **Origin Access Control (Recommended)**
5. Save → Create distribution.

This makes CloudFront the only allowed service that can read your bucket.

---

## **3️⃣ Apply Bucket Policy to Allow CloudFront Access**

Use the policy below (this is the final working version for your case):

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowCloudFrontOACAccess",
      "Effect": "Allow",
      "Principal": {
        "Service": "cloudfront.amazonaws.com"
      },
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::buckerr989898/*",
      "Condition": {
        "StringEquals": {
          "AWS:SourceArn": "arn:aws:cloudfront::526888234336:distribution/EBEJZQVVTMTHA"
        }
      }
    }
  ]
}
```

### ✔ This policy allows CloudFront to read:

* `index.html`
* `assets/**`
* `images/**`
* Any future files inside the bucket

Because the `/*` wildcard covers **all folders** inside S3.

---

## **4️⃣ Disable S3 Public Access Permissions**

You **must not make your files public manually**.

Leave:

* **Bucket Public Access Block = ON**
* No public ACLs
* No public objects

CloudFront → OAC → is responsible for access.

---

## **5️⃣ Invalidate CloudFront Cache**

After uploading new files or updating the policy:

Go to:
**CloudFront → Invalidations → Create Invalidation → `/*`**

This forces CloudFront to fetch fresh content.

---

## **6️⃣ Test the Website**

Open your CloudFront domain:

```
https://dxxxxxxxxxxxx.cloudfront.net
```

Your entire website (HTML + assets + images) will now load even though the S3 bucket is **fully private**.

---

# 📝 **Summary for Notes**

### ✔ Private S3 bucket

Public access is blocked.

### ✔ CloudFront OAC

Securely accesses the bucket on your behalf.

### ✔ Bucket Policy

Allows CloudFront distribution to read all files.

### ✔ CloudFront Domain

Used to load the website.

### ✔ Static Website Hosting

Not required — CloudFront reads objects directly.

---

# 🎉 Final Output

With this configuration:

* Website loads from CloudFront
* S3 bucket stays **100% private**
* All folders (`assets/`, `images/`, etc.) work
* No 403 errors

---
