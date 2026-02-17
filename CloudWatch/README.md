# 🔍 Amazon CloudWatch (Monitoring & Alarms)

![AWS GIF](https://github.com/shyamdevk/AWS-Concepts-Labs/blob/images/cloudwatch.gif)

### 🧠 Definition
**CloudWatch** monitors AWS resources and applications in real-time — providing metrics, logs, dashboards, and automated actions.

---

## 🎯 Key Features

| Feature | Description |
|----------|--------------|
| **Metrics** | Data points measuring performance (CPU, Network, Disk) |
| **Alarms** | Trigger actions when thresholds are crossed |
| **Logs** | Store application/system logs |
| **Dashboards** | Visual display of metrics |
| **Events / Rules** | Automated responses to resource changes |

---

## 💡 How CloudWatch Works

1. Collects metrics from AWS services (EC2, RDS, Lambda)
2. Creates alarms for thresholds
3. Alarm can:
   - Send SNS alert  
   - Trigger Auto Scaling  
   - Invoke Lambda functions  

---

## 🧪 Example Scenario
CloudWatch monitors CPU utilization:
- If CPU > 80% → Alarm activates
- ASG adds a new EC2 instance

---

# 🧭 LAB — Monitor EC2 with CloudWatch

### 🪜 Steps:

1. Launch EC2 → Enable **Detailed Monitoring**  
2. Open CloudWatch → Metrics → EC2 → Select your Instance ID  
3. Choose a metric (e.g., **CPUUtilization**)  
4. Generate stress:
   ```bash
   sudo yum install stress -y
   stress --cpu 20 --timeout 300

5. Observe CloudWatch → CPU usage spikes in few minutes.

✅ Verified CloudWatch metric collection.

---

# ⚙️ LAB — Target Tracking Policy

**Goal:** Trigger an alarm automatically and scale ASG based on CPU.

### 🪜 Steps:

1. Create Launch Template → Enable **CloudWatch Monitoring**
2. Launch ASG → Configure **Health Check Period**
3. Go to ASG → **Automatic Scaling Policy → Create Dynamic Policy**
 ![Screenshot](https://github.com/shyamdevk/AWS-Concepts-Labs/blob/images/29.png)
4. A CloudWatch **Alarm** will be automatically created.
 ![Screenshot](https://github.com/shyamdevk/AWS-Concepts-Labs/blob/images/30.png)
5. From EC2 CLI, generate load:

   ```bash
   stress --cpu 60 --timeout 300
   ```
6. When CPU > Target (e.g., 50%), the alarm triggers, and a new EC2 instance is added.

✅ Verified automatic scaling response.

---

# 🚨 Amazon CloudWatch Alarm

### 🧠 What is an Alarm?

A **CloudWatch Alarm** monitors a specific metric and triggers actions when the threshold is breached.

---

### 📊 Alarm States

| State                 | Meaning                   |
| --------------------- | ------------------------- |
| **OK**                | Everything is normal      |
| **ALARM**             | Metric crossed threshold  |
| **INSUFFICIENT DATA** | Not enough data collected |

---

### ⚙️ Alarm Conditions

| Parameter | Example         |
| --------- | --------------- |
| Metric    | CPUUtilization  |
| Statistic | Average         |
| Operator  | >, <, ≥, ≤      |
| Threshold | 80              |
| Period    | Every 5 minutes |

---

## 🧭 LAB — Setup Alarm for EC2 Metric

1. Launch instance → Enable **Detailed Monitoring**
2. CloudWatch → All Metrics → EC2 → Per-Instance metrics
3. Select your instance → Metric (CPUUtilization)
 ![Screenshot](https://github.com/shyamdevk/AWS-Concepts-Labs/blob/images/31.png)
4. Create Alarm → Configure threshold → Skip action
5. Add details → Create Alarm
6. Generate stress:

   ```bash
   stress --cpu 20 --timeout 300
   ```

✅ Alarm triggers when CPU crosses the defined threshold.

---

# ⚡ Simple Scaling Policy

**Simple Scaling** = One CloudWatch alarm triggers one scaling action.

---

### 🧩 Example Flow

1. Alarm: CPU > 80% → Add 1 instance
2. Cooldown period (stabilize)
3. Alarm: CPU < 40% → Remove 1 instance

---

### 🧭 LAB — Simple Scaling

1. Create Launch Template (enable monitoring)
2. Launch ASG
3. Create CloudWatch Alarm
4. Create Policy:

   * `ASG → Edit → Dynamic Scaling Policy`
   * Link alarm to scale-out action
 ![Screenshot](https://github.com/shyamdevk/AWS-Concepts-Labs/blob/images/32.png)
5. Generate stress to trigger alarm:

   ```bash
   stress --cpu 60 --timeout 300
   ```

✅ Instance added automatically after alarm triggers.

---

# ⚙️ Step Scaling Policy

**Step Scaling** = Adds or removes instances **gradually**, depending on how much the metric crosses the threshold.

---

### 🧠 Example

| CPU Utilization | Action            |
| --------------- | ----------------- |
| >60%            | Add 1 instance    |
| >80%            | Add 2 instances   |
| <40%            | Remove 1 instance |

---

### 🧭 LAB — Step Scaling Policy

1. Launch Template → Enable CloudWatch Monitoring
2. Launch ASG
3. Create Alarm → Add **SNS Notification Topic (Email)**

   * Subscribe to the topic (check spam folder)
4. Create Dynamic Policy:
 ![Screenshot](https://github.com/shyamdevk/AWS-Concepts-Labs/blob/images/33.png)
   * Step 1 → Add instance if CPU >50%
   * Step 2 → Add another instance if CPU >60%

✅ ASG scales stepwise according to CPU load.

---

# 🌩️ Summary

| Feature                | Purpose                                       |
| ---------------------- | --------------------------------------------- |
| **Load Balancer**      | Distributes traffic across EC2 instances      |
| **Auto Scaling Group** | Automatically manages instance count          |
| **Launch Template**    | Predefined EC2 configuration for ASG          |
| **Scaling Policies**   | Define when/how scaling occurs                |
| **CloudWatch**         | Monitors metrics and triggers scaling actions |

---
# ☁️ Amazon CloudWatch — Advanced Concepts  

> A complete continuation guide covering **CloudWatch Alarms, Dashboards, and Grafana Integration** for advanced AWS monitoring and visualization.

---

# 🧠 What is Amazon CloudWatch?

**Amazon CloudWatch** is a real-time monitoring and observability service that tracks metrics, logs, and events from AWS resources and applications.

It helps you:
- Monitor performance (CPU, Memory, Disk, Network)
- Create alarms and automated actions
- Visualize metrics through dashboards
- Integrate with external monitoring tools like **Grafana**

---

# 🚨 CloudWatch Alarms

### 1️⃣ What is a CloudWatch Alarm?

A **CloudWatch Alarm** continuously monitors metrics and **triggers actions** when a specified threshold is crossed.

**Example:**
> Trigger an alarm when CPU Utilization > 80% for 5 minutes.

---

## 📌 Types of CloudWatch Alarms

### 🔹 1. Metric Alarm
- Monitors a **single metric** (e.g., `CPUUtilization`, `DiskReadOps`, `NetworkIn`)
- Triggers when metric value exceeds or drops below a defined threshold.
- Most commonly used alarm type.

**Example:**
```text
Alarm: CPUUtilization > 80%
Action: Send notification or scale EC2
````

---

### 🔹 2. Composite Alarm

* Combines multiple **metric alarms** using logical operators (**AND / OR**)
* Useful for reducing **false positives**

**Example:**

> Alarm triggers only if:
> `CPU > 80% AND Memory > 75%`

✅ Great for multi-metric monitoring (e.g., EC2 + RDS health checks).

---

### 🔹 3. Anomaly Detection Alarm

* Uses **Machine Learning** to automatically detect normal metric patterns.
* Alarm triggers when the metric value moves outside the predicted range.
* Ideal for **unpredictable or fluctuating workloads**.

**Example:**

> Detect unusual network spikes or CPU anomalies automatically.

---

# 📊 CloudWatch Dashboard

### 🧠 Definition

A **CloudWatch Dashboard** is a customizable visual panel to view multiple metrics and alarms in one place.

You can add:

* 📈 CPU Utilization graphs
* 🌐 Network Traffic trends
* 💾 Disk I/O metrics
* ⚠️ Alarm statuses
* 🧾 Log insights
* 💡 Custom Metrics

---

### 💡 Why Use a Dashboard?

| Benefit                  | Description                                           |
| ------------------------ | ----------------------------------------------------- |
| **Centralized View**     | Monitor all key AWS metrics in one console            |
| **Real-Time Monitoring** | Track EC2, RDS, and ELB performance instantly         |
| **Troubleshooting**      | Identify issues faster using visual data              |
| **Custom Metrics**       | Display app-specific data or CloudWatch Logs insights |

---

# 🧭 LAB — Create Custom Dashboard for an Instance

### 🪜 Steps:

1. **Launch an EC2 Instance**

   * Enable **Detailed Monitoring** for CloudWatch.

2. **Navigate to CloudWatch:**

   * Open: `CloudWatch → Dashboards → Create Dashboard`

3. **Customize Your Dashboard:**

   * Choose visualization type (Line, Stacked, Number, Text)
   * Select Metrics → EC2 → Per-Instance Metrics
   * Choose metrics such as:

     * CPUUtilization
     * NetworkIn / NetworkOut
     * DiskReadOps / DiskWriteOps

4. Add widgets as needed → Save Dashboard.

✅ You now have a real-time custom dashboard for your instance!

---

# 📉 LAB — Visualize CloudWatch Data Using Grafana

**Goal:** Use Grafana to display CloudWatch metrics visually using real-time dashboards.

---

### ⚙️ Requirements

* **2 EC2 Instances**

  * One for **Grafana Server**
  * One for **Monitoring (Target Instance)**

---

### 🪜 Steps:

#### Step 1 — Install Grafana

1. SSH into the **Grafana Instance**
2. Install Grafana (follow official instructions):

   ```bash
   sudo yum install grafana -y
   sudo systemctl start grafana-server
   sudo systemctl enable grafana-server
   ```
3. Access Grafana in your browser:

   ```
   http://<Grafana-Instance-Public-IP>:3000
   ```

   * Default credentials:

     ```
     Username: admin
     Password: admin
     ```

---

#### Step 2 — Connect Grafana to CloudWatch

1. Go to:
   `Grafana → Connections → Data Sources → Add Data Source → Amazon CloudWatch`

2. Under **Authentication Provider**, select:

   ```
   Access & Secret Key
   ```

3. Provide:

   * **Access Key ID**
   * **Secret Access Key**
     (You can get these from the **Root User** or **IAM User**)

4. Click **Save & Test**

---

#### Step 3 — IAM Role & Permissions

* Ensure your **Grafana Instance** has an **IAM Role** attached with:

  ```
  CloudWatchFullAccess
  ```
* Attach via:

  ```
  EC2 → Actions → Security → Modify IAM Role
  ```

✅ Grafana can now access CloudWatch metrics.

---

#### Step 4 — Create Grafana Dashboard

1. In Grafana, go to:

   ```
   Dashboards → Create New Dashboard → Add Visualization
   ```
2. Choose **Data Source: CloudWatch**
3. Add metrics such as:

   * CPUUtilization
   * NetworkIn / NetworkOut
   * Disk I/O
   * Latency / Requests for ALB
4. Customize visualization types (Graphs, Gauges, Heatmaps, etc.)

✅ Your Grafana Dashboard now displays live CloudWatch metrics!

---

# 💡 Benefits of Using Grafana with CloudWatch

| Feature                  | Description                                       |
| ------------------------ | ------------------------------------------------- |
| **Rich Visualizations**  | Beautiful charts and dashboards                   |
| **Multi-Source Support** | Combine AWS CloudWatch with other data sources    |
| **Alerting System**      | Create advanced alerts beyond CloudWatch defaults |
| **Team Collaboration**   | Share dashboards easily across teams              |
| **Real-Time Analytics**  | Live data from AWS services                       |

---

# 📦 Summary

| Concept                     | Description                                         |
| --------------------------- | --------------------------------------------------- |
| **Metric Alarm**            | Monitors single metric for threshold breaches       |
| **Composite Alarm**         | Combines multiple alarms using AND/OR logic         |
| **Anomaly Detection Alarm** | Detects unusual behavior using ML                   |
| **CloudWatch Dashboard**    | Centralized visual display of all metrics           |
| **Grafana Integration**     | External visualization tool connected to CloudWatch |

---
