# **🛡️ AWS WAF – Web Application Firewall**

Amazon Web Application Firewall (**AWS WAF**) protects your **web applications** from common internet attacks.
Think of it as a **security guard standing in front of your website**, checking every request and blocking the bad ones.

---

## 📌 What is AWS WAF?

AWS WAF (Web Application Firewall) helps protect:

* Web applications
* APIs
* Websites

by filtering incoming traffic and blocking harmful requests.

---

## 🎯 Why Use AWS WAF?

AWS WAF protects your application from:

* ❌ **SQL Injection (SQLi)**
* ❌ **Cross-Site Scripting (XSS)**
* ❌ **Bad Bots**
* ❌ **DDoS (Layer 7)**
* ❌ **IP-based attacks**
* ❌ **Suspicious or unwanted traffic**

✔ Helps improve security
✔ Maintains availability
✔ Protects your APIs and websites

---

## 🏗️ Where Can You Use AWS WAF?

AWS WAF can be attached to:

* 🌐 **CloudFront** (CDN)
* ⚖️ **Application Load Balancer (ALB)**
* 🔌 **API Gateway**
* 📡 **AWS AppSync (GraphQL)**

---

## 🧱 Core Components of AWS WAF

### 1️⃣ **Web ACL (Access Control List)**

This is the **main firewall**.
Contains all rules to ALLOW, BLOCK, or COUNT requests.

### 2️⃣ **Rules**

Define what traffic to block or allow.
Examples:

* Block IPs
* Allow internal IPs
* Detect SQLi / XSS
* Filter based on headers / URIs

### 3️⃣ **Rule Groups**

A collection of rules bundled together.
You can use:

* **AWS Managed Rule Groups** (recommended)
* **Custom Rule Groups**

### 4️⃣ **Managed Rules**

Prebuilt protection by AWS including:

* SQL Injection protection
* XSS protection
* Bad Bot detection
* Known attack signatures

Easy to use: **just enable them**.

---

## 🔍 How AWS WAF Works (Simple Flow)

1. A user sends a request to your application
2. Request first goes through **AWS WAF**
3. WAF checks rules inside the **Web ACL**
4. WAF decides:

   * ✔ **Allow**
   * ❌ **Block**
   * 📊 **Count (log only)**

---

## 🧪 Examples of Common Rules

### ✔ Block specific IPs

Example: Block an attacker’s IP.

### ✔ Allow only office IPs

Helpful for internal apps.

### ✔ Block SQL Injection automatically

Use AWS managed rules.

### ✔ Rate Limiting

Example: Block users sending
**more than 100 requests in 5 minutes**.

---

## 💲 AWS WAF Pricing (Simple)

You pay for:

* Number of **Web ACLs**
* Number of **Rules**
* Number of **Requests inspected**

No upfront cost → Pay only for what you use.

---

## 📊 Monitoring AWS WAF

You can monitor traffic using:

* **CloudWatch Metrics**
* **AWS WAF Logs**
* **Kinesis**
* **S3 log storage**
* **CloudWatch Dashboards**

You can see:

* Blocked Requests
* Allowed Requests
* Which rule triggered
* Traffic patterns

---

## 🧠 Advantages of AWS WAF

* Easy to setup
* Protects from common attacks
* Auto scales with your traffic
* Integrates well with CloudFront, ALB, API Gateway
* Managed rules save time
* Improves website & API security

---

## 📌 Quick Summary

AWS WAF protects your web applications by:

* Checking incoming traffic
* Blocking harmful requests
* Allowing only safe traffic

It uses **Web ACLs** + **Rules** and integrates with **CloudFront, ALB, and API Gateway**.
Easy to use, powerful, and scalable.

---

# 🛡️ AWS WAF Hands-On Lab (Attach to ALB with Custom Rules)

This lab walks you through creating an **AWS WAF Web ACL**, adding **custom rules**, creating an **IP Set**, and attaching WAF to an **Application Load Balancer (ALB)**.

> 🔔 **Note:**
> VPC, EC2 instance, User Data web server, and ALB creation are **already covered**.
> This focuses **only on AWS WAF steps**.

---

## 📌 **Lab Overview**

You will create:

* 🌐 **Web ACL**
* 📘 **IP Set** (for your IP address)
* 🚫 **Block Rule**
* 🔐 **Captcha Rule**
* 🔗 **Attach WAF to ALB**

---

# 🟦 1. Open AWS WAF Console

1. Go to AWS Console
2. Search **WAF**
3. Open **AWS WAF & Shield**

---

# 🟩 2. Create an IP Set (Add Your IP)

This IP Set will contain **your public IP** → so you can be allowed.

1. Left menu → **IP addresses**
2. Click **Create IP set**
3. Enter:

   * **Name:** `My-IP-Set`
   * **Region:** Same as ALB
4. Under **IP addresses**, enter your IP:

   ```
   <your-public-ip>/32
   ```

   Example:

   ```
   49.37.171.28/32
   ```
5. Click **Create IP set**

✔ Your IP Set is now ready.

---

# 🟧 3. Create a Web ACL

1. Left menu → **Web ACLs**
2. Click **Create web ACL**
3. Enter:

   * **Name:** `My-Web-ACL`
   * **Region:** Same as ALB
4. **Resource Type:**

   ```
   Application Load Balancer
   ```
5. Select **your ALB**
6. Click **Next**

---

# 🟥 4. Add Rules to Web ACL

You will add three rules:

1. **Allow your IP**
2. **Block a sample IP range**
3. **Captcha for unknown/high-rate traffic**

---

## ✳️ Rule 1 — Allow Your IP

1. Click **Add rule → Add my own rules and rule groups**
2. Select **Rule builder**
3. Rule name: `Allow-My-IP`
4. Rule type: **IP match**
5. Choose **IP set** → `My-IP-Set`
6. Action → **Allow**
7. Save rule

✔ You will always be allowed.

---

## ✳️ Rule 2 — Block a Test IP Range

(Use ANY IP range for testing.)

1. Add rule → Rule builder
2. Rule name: `Block-Test-IP`
3. Match type: **IP match**
4. Create new IP set (example):

   ```
   1.1.1.0/24
   ```
5. Action → **Block**
6. Save rule

✔ Any request from that range will be blocked.

---

## ✳️ Rule 3 — Captcha High Request Traffic

1. Add rule → **Rule builder**
2. Name: `Captcha-Unknown-IPs`
3. Rule type: **Rate-based rule**
4. Limit:

   ```
   100 requests per 5 minutes
   ```
5. Action → **Captcha**
6. Save rule

✔ High-frequency traffic will get a CAPTCHA.

---

# 🟪 5. Set Rule Priority (ORDER IS IMPORTANT)

Drag and arrange rules:

```
1️⃣ Allow-My-IP
2️⃣ Block-Test-IP
3️⃣ Captcha-Unknown-IPs
```

Default Action → **Allow**
(Recommended for labs)

---

# 🟦 6. Review & Create

1. Click **Next**
2. Review all rules
3. Click **Create Web ACL**

✔ WAF is now attached to your ALB with all rules active.

---

# 🟨 7. Testing the WAF Rules

### ✅ Test Allow Rule

Open your ALB DNS in your browser:

```
http://<ALB-DNS>
```

Your IP should load the website normally.

---

### ❌ Test Block Rule

Try accessing from:

* VPN
* Proxy
* Mobile hotspot
* Online IP testing tool

A blocked IP should receive **403 Forbidden**.

---

### 🔐 Test Captcha Rule

Refresh your browser very fast or use a simple curl loop →
You should eventually see a **CAPTCHA challenge**.

---

# 🎉 **Lab Completed Successfully!**

You have implemented:

* ✔ IP Set
* ✔ Web ACL
* ✔ Allow, Block & Captcha rules
* ✔ WAF attached to ALB
* ✔ Practical testing

---
