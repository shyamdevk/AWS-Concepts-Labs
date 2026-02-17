## 📘 **What is CORS?**
![Screenshot](https://github.com/shyamdevk/AWS-Concepts-Labs/blob/images/cors.png)

**CORS (Cross-Origin Resource Sharing)** is a browser security feature that decides **which websites are allowed to access your AWS resources** (like S3, API Gateway, CloudFront).

👉 If your frontend and backend are on **different domains**, the browser checks CORS.

---

# 🔍 **Why CORS Exists?**

Browsers block requests between different websites to protect users from malicious sites.
So AWS resources must explicitly say:

> **“Yes, this website is allowed to access me.”**

---

# 🟦 **When Does CORS Trigger?**

CORS is checked when:

* `frontend.com` → calls → `api.com`
* `localhost:3000` → calls → AWS API Gateway
* CloudFront → loads image from S3
* Any **cross-domain** request happens

If CORS is not allowed → browser blocks.

---

# 📌 **Important CORS Concepts**

### **1. Origin**

Origin = Protocol + Domain + Port
Example:

```
https://example.com:443
```

### **2. Access-Control-Allow-Origin**

Specifies **which websites** can access the resource.

Example (allow one site):

```
Access-Control-Allow-Origin: https://myapp.com
```

Allow all sites:

```
Access-Control-Allow-Origin: *
```

### **3. Access-Control-Allow-Methods**

Allowed HTTP methods:

```
GET, POST, PUT, DELETE, OPTIONS
```

### **4. Access-Control-Allow-Headers**

What headers the frontend can send:

```
Content-Type, Authorization
```

### **5. Preflight Request**

Before sending actual data, browser sends:

```
OPTIONS request
```

to check if the server allows it.

If server responds with proper CORS headers → request continues.

---

# 🛠️ **CORS in AWS Services**

## ✔️ **1. S3 (Static Websites, Images, Files)**

To allow any website to access S3 objects:

```xml
<CORSRule>
  <AllowedOrigin>*</AllowedOrigin>
  <AllowedMethod>GET</AllowedMethod>
  <AllowedHeader>*</AllowedHeader>
</CORSRule>
```

If you want ONLY your site:

```xml
<CORSRule>
  <AllowedOrigin>https://mywebsite.com</AllowedOrigin>
  <AllowedMethod>GET</AllowedMethod>
  <AllowedHeader>*</AllowedHeader>
</CORSRule>
```

---

## ✔️ **2. API Gateway (Frontend → API Calls)**

When frontend calls API Gateway, you must enable CORS.

Typical API Gateway response headers:

```
Access-Control-Allow-Origin: *
Access-Control-Allow-Methods: GET,POST,OPTIONS
Access-Control-Allow-Headers: Content-Type
```

API Gateway handles OPTIONS (preflight) automatically if CORS is enabled.

---

## ✔️ **3. CloudFront (CDN for S3/API)**

CloudFront must forward these headers to origin:

```
Origin
Access-Control-Request-Headers
Access-Control-Request-Method
```

Otherwise CORS fails even if S3/API is configured correctly.

---

# 🎯 **Simple Example to Understand**

### Frontend:

```
http://localhost:3000
```

### API (AWS):

```
https://abc123.execute-api.ap-south-1.amazonaws.com
```

Since domains are different → Browser blocks by default.

To allow access, API must return:

```
Access-Control-Allow-Origin: http://localhost:3000
```

Now browser allows the request.

---

# ⚠️ **Common CORS Errors**

| Error                                     | Meaning                                      |
| ----------------------------------------- | -------------------------------------------- |
| **No Access-Control-Allow-Origin header** | CORS not enabled or wrong domain             |
| **Blocked by CORS policy**                | Browser rejected the request                 |
| **Preflight request failed (OPTIONS)**    | Backend did not respond correctly to OPTIONS |

---

# 🧠 **Super Simple Summary**

* CORS = Browser permission system
* AWS services must say **which websites can access them**
* Used in **API Gateway, S3, CloudFront**
* Without CORS → Browser blocks request
* Always configure correct **origin, methods, headers**

---

# 🧪 **LAB: Access S3 Image Using CORS + Load on Button Click**

## 🎯 **Goal**

Set up an S3 bucket with correct **CORS**, **permissions**, and **public access** so a simple `index.html` file can load an image from S3 **only after pressing a button**.

---

# ✅ **Step 1: Create S3 Bucket**

1. Go to **AWS Console → S3**
2. Click **Create bucket**
3. Bucket name:

   ```
   my-cors-demo-bucket-009
   ```
4. Leave all default settings
5. Click **Create bucket**

---

# ✅ **Step 2: Upload Image**

1. Open the bucket
2. Click **Upload**
3. Choose your file, example:

   ```
   image.png
   ```
4. Click **Upload**

---

# ✅ **Step 3: Fix Access Denied (Make Objects Public)**

### 🔹 Turn OFF Block Public Access

Go to:

**Bucket → Permissions → Block Public Access → Edit**

Turn off:

* Block all public access
* Block public ACLs
* Block public bucket policies

Save.

---

### 🔹 Add Public Read Bucket Policy

Go to:

**Bucket → Permissions → Bucket Policy → Edit**

Paste:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowPublicRead",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::my-cors-demo-bucket-009/*"
    }
  ]
}
```

Save changes.

---

# ✅ **Step 4: Add Correct JSON CORS Configuration**

Go to:

**Bucket → Permissions → CORS Configuration → Edit**

Paste this:

```json
[
  {
    "AllowedHeaders": ["*"],
    "AllowedMethods": ["GET"],
    "AllowedOrigins": ["*"],
    "ExposeHeaders": []
  }
]
```

✔ Fixes CORS issues
✔ Lets any frontend load GET objects

---

# ✅ **Step 5: Test S3 URL**

Open your uploaded image:

```
https://my-cors-demo-bucket-009.s3.amazonaws.com/image.png
```

If the image displays → permissions and CORS are correct.

---

# ✅ **Step 6: Create index.html (Load Image on Button Click)**

Create a file named **index.html**:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Load S3 Image on Button Click</title>
</head>
<body style="font-family: Arial; text-align: center; margin-top: 50px;">

    <h2>Load Image from S3 using Button</h2>

    <button onclick="loadImage()" style="padding: 10px 20px; font-size: 16px;">
        Load Image
    </button>

    <br><br>

    <img id="s3Image" src="" alt="Image will load here" width="300"
         style="display:none; border:1px solid #ddd; padding:10px;">

    <script>
        function loadImage() {
            const imageUrl = "https://my-cors-demo-bucket-009.s3.amazonaws.com/image.png";
            const img = document.getElementById("s3Image");

            img.src = imageUrl;
            img.style.display = "block";
        }
    </script>

</body>
</html>
```

---

# ✅ **Step 7: Run the Test**

1. Open `index.html` in your browser
2. Click **Load Image**
3. You should now see the S3 image appear without errors

---
!

## 📘 **VPN in VPC**

A **VPN (Virtual Private Network)** in AWS securely connects your **on-premises datacenter** or **office network** to your **AWS VPC** using encrypted tunnels over the internet.
This allows both networks to communicate **as if they were within the same private network**.

---

## 🧱 **Key Components**

### **1️⃣ VPC**

Your private network inside AWS.

### **2️⃣ Virtual Private Gateway (VGW)**

* AWS-side VPN endpoint
* Must be **attached to your VPC**

### **3️⃣ Customer Gateway (CGW)**

* Represents your on-premises router/firewall
* Requires a **public IP address**

### **4️⃣ VPN Connection**

* Creates the secure IPsec tunnel between CGW ↔ VGW
* AWS provides **two tunnels** for high availability

---

## 🌐 **How VPN Works (Simple Flow)**

1. Create a **Virtual Private Gateway (VGW)**
2. Attach the VGW to your **VPC**
3. Create a **Customer Gateway (CGW)** using your router’s public IP
4. Create a **VPN Connection** linking VGW and CGW
5. Download VPN configuration and apply it to your on-prem router
6. Traffic starts flowing **securely** through encrypted tunnels

---

## 🔑 **Why Use VPN in VPC?**

* Secure communication between **AWS and on-premises**
* Easy and fast to set up
* Low cost compared to Direct Connect
* Provides **hybrid cloud** capability

---

## 🚀 **Types of VPN in AWS**

### **🔹 Site-to-Site VPN**

Connects entire on-prem network to AWS VPC
(Most commonly used)

### **🔹 Client VPN**

Allows individual users to connect securely from laptops or mobile devices.

### **🔹 AWS CloudHub**

Used to connect multiple branch locations together using AWS.

---

## ✔️ **Benefits**

* Encrypted traffic over internet
* Two tunnels = High availability
* Low cost and quick setup
* Works with most routers

---

## ⚠️ **Limitations**

* Depends on internet reliability
* Higher latency compared to Direct Connect
* Throughput limits (~1.25 Gbps)

---

## 📝 **Short Summary for Quick Revision**

```
VPN in VPC provides a secure, encrypted tunnel between on-premises networks and AWS VPC using a Virtual Private Gateway and Customer Gateway. It enables hybrid cloud connectivity, supports two tunnels for redundancy, and is cost-effective and easy to configure.
```
