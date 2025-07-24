# 🚀 Auto Scaling Groups (ASG) – AWS Exam Notes

---

## 📌 What is an Auto Scaling Group?

An **Auto Scaling Group (ASG)** helps manage **EC2 instance count automatically** based on **demand**, improving **resilience, cost-efficiency, and scalability**.

### 🧭 ASG Key Goals:
- **Scale Out**: Add EC2 instances as load increases.
- **Scale In**: Remove EC2 instances as load decreases.
- **Minimum/Maximum/Desired** instance thresholds.
- **Automatic registration** of EC2s to Load Balancer.
- **Self-healing**: Replace failed/unhealthy EC2 instances.
- **No cost for ASG itself** – only pay for EC2, EBS, etc.

---

## 🛠️ ASG Configuration – Core Components

### 🔧 Launch Template (modern standard)
Defines how instances in ASG are configured. Includes:
- Amazon Machine Image (AMI)
- Instance type
- EC2 User Data (bootstrap scripts)
- EBS volumes
- Security Groups
- SSH Key Pair
- IAM Role for EC2
- Network (VPC, subnets)
- Load Balancer details (ALB/NLB)
- Tags and metadata

> ✅ *Note*: Launch Configurations are deprecated. Always use **Launch Templates**.

---

## ⚙️ ASG Capacity Settings

- **Minimum capacity**: Minimum number of EC2 instances at all times.
- **Desired capacity**: Default/target number of instances the ASG tries to maintain.
- **Maximum capacity**: Upper bound limit for scaling out.

---

## 📈 ASG Scaling Policies

Auto Scaling policies define how ASG reacts to changes in metrics:

### 🔄 1. **Dynamic Scaling**
- **Target Tracking Scaling**
  - Easiest to set up.
  - Example: Keep average CPU at 40%.
- **Simple/Step Scaling**
  - Trigger actions when thresholds are crossed.
  - Ex: CPU > 70% → Add 2 instances.
  - Ex: CPU < 30% → Remove 1 instance.

### 📅 2. **Scheduled Scaling**
- Predetermined scaling at specific times.
- Ex: Increase desired capacity to 10 every Friday at 5 PM.

### 🔮 3. **Predictive Scaling**
- Machine Learning-based forecasting of load.
- Automatically schedules scale-in/out based on future demand.

---

## 📊 Metrics for Auto Scaling

ASG uses **Amazon CloudWatch** alarms to monitor and act on metrics:

| Metric Name            | Use Case                                 |
|------------------------|------------------------------------------|
| `CPUUtilization`       | Common for compute-bound apps            |
| `RequestCountPerTarget`| Balancing HTTP requests across instances |
| `NetworkIn/Out`        | Network-intensive apps                   |
| Custom Metric          | App-specific logic (e.g., active users)  |

> 🔧 **Custom Metric Scaling**:
> 1. Push data using `PutMetricData` API.
> 2. Create CloudWatch alarm.
> 3. Attach to scaling policy in ASG.

---

## 🛑 Cooldown Periods

- **Cooldown**: After scaling activity, prevents new actions until metrics stabilize.
- Default: `300 seconds` (can be customized).
- Tip: Use **pre-baked AMIs** to reduce warm-up times and shorten cooldown.

---

## 🔁 Instance Refresh

Used to **update running instances** to match a new Launch Template version.

- Gradually replaces all instances.
- Parameters:
  - **Minimum healthy percentage**
  - **Warm-up time** (before instance counts as healthy)

---

## 🌐 ASG with Load Balancer

### Typical Architecture:
- **Elastic Load Balancer (ALB/NLB)** in front of ASG.
- Distributes traffic and performs **health checks**.
- ASG **deregisters unhealthy EC2** instances and **replaces them**.

---

## 💡 ASG Exam Tips

- ✅ ASG is free – you only pay for underlying resources.
- ✅ Launch Templates are **required** (not configurations).
- ✅ Use **CloudWatch alarms** to trigger scale in/out.
- ✅ Supports **predictive**, **target tracking**, and **scheduled** scaling.
- ✅ ASG ensures high availability and fault tolerance.
- ✅ Works great with **stateless EC2 apps** behind **ALB**.
- ✅ **IAM Role** defined in the Launch Template is applied to all EC2s.
- ✅ Always consider **multi-AZ deployments** for resilience.

---

## 🧠 Summary Table

| Feature                | Description                                      |
|------------------------|--------------------------------------------------|
| Launch Template        | Defines EC2 config (AMI, type, user data, etc.) |
| Capacity               | Min / Max / Desired instances                    |
| Scaling Policies       | Dynamic, Scheduled, Predictive                   |
| Cooldown               | Wait time after scaling                          |
| Health Management      | Replace unhealthy instances automatically        |
| Load Balancer Support  | Automatic registration/deregistration            |
| Cost                   | ASG free – pay for EC2 only                      |

---

