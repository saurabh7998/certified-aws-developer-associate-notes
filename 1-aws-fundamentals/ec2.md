# 🖥️ Amazon EC2 – Elastic Compute Cloud

Amazon EC2 (Elastic Compute Cloud) is one of the most foundational AWS services. It lets you provision **virtual machines** in the cloud, with flexible configuration, scaling, pricing, and control.

---

## ⚙️ What Does EC2 Offer?

- Launch **virtual servers** (EC2 instances)
- Attach **block storage** (EBS)
- Use **ELB** for traffic distribution
- Enable **Auto Scaling Groups (ASG)** for horizontal scaling

> 📌 EC2 is part of the **Infrastructure-as-a-Service (IaaS)** model

---

## 📦 EC2 Instance Configuration Options

| Element                | Description                                                                 |
|------------------------|-----------------------------------------------------------------------------|
| Operating System       | Linux, Windows, macOS                                                       |
| CPU / vCPUs            | Number of virtual cores                                                     |
| RAM                    | Memory in GiB                                                               |
| Storage                | EBS or ephemeral Instance Store                                             |
| Networking             | Public/Private IP, Bandwidth, ENI                                           |
| Security               | Controlled via **Security Groups**                                          |
| Key Pair               | SSH Key for secure access                                                   |
| Bootstrap Script       | EC2 **User Data** to auto-configure software at launch                      |

---

## 🚀 EC2 Instance Launch Types

| Type                      | Description                                                                            |
|---------------------------|----------------------------------------------------------------------------------------|
| On-Demand                 | Pay by the second, no upfront cost, highest flexibility                               |
| Reserved Instances        | 1–3 year commitment, up to 75% savings                                                 |
| Convertible Reserved      | Can switch instance families/types                                                    |
| Scheduled Reserved        | Launch during reserved time blocks                                                    |
| Spot Instances            | Up to 90% cheaper, can be terminated with 2 min warning                               |
| Dedicated Instances       | Run on hardware isolated to your account                                              |
| Dedicated Hosts           | Full physical server access (compliance or licensing constraints)                     |

---

## 🧱 EC2 Instance Types Overview

Each EC2 instance has characteristics:

| Factor        | Description                                      |
|---------------|--------------------------------------------------|
| Compute       | vCPU, generation                                 |
| Memory        | RAM amount/type                                  |
| Storage       | EBS / Ephemeral / NVMe                           |
| Networking    | Bandwidth, latency                               |
| GPU           | ML, rendering, HPC                               |

🔗 https://aws.amazon.com/ec2/instance-types/  
🔗 https://instances.vantage.sh

---

## 🧠 EC2 Family Examples

| Family | Optimized For              | Example Use Cases                            |
|--------|----------------------------|-----------------------------------------------|
| T      | Burstable General Purpose  | Low consistent usage, test/dev               |
| M      | Balanced                   | Web servers, app servers                     |
| C      | Compute Optimized          | Gaming servers, scientific modeling          |
| R      | Memory Optimized           | Caches, large DBs, BI workloads              |
| I      | Storage Optimized          | NoSQL, OLTP, analytics                       |
| G/F/P  | GPU-based                  | ML training, AI inference, rendering         |
| X/Z/H  | High memory/custom         | SAP, specialized enterprise workloads        |

---

## 💥 Burstable Instances – T Series

### T2/T3 Overview

- Accumulate **CPU credits**
- Credits consumed when bursting beyond baseline

### T2 Unlimited Mode

- Continue bursting **even when out of credits**
- Pay extra for exceeded usage
- Ideal for workloads with unexpected spikes

---

## 🔧 EC2 User Data

- Used to **bootstrap** an instance at launch (runs **once only**)
- Runs as **root user**
- Ideal for:
  - Installing packages
  - Running startup scripts
  - Downloading files from the internet

---

## 📡 EC2 Meta Data

- Accessible from within the instance:  
  `http://169.254.169.254/latest/meta-data/`

- Useful to retrieve:
  - Instance ID
  - AMI ID
  - Security Group
  - IAM Role (but not IAM Policy)
  - Public/Private IPs

---

## 🔐 Security Groups

- Act as **virtual firewalls** for EC2
- Control **inbound/outbound traffic**
- Attached to **ENI**, not the instance directly
- Stateless → Must define **both inbound & outbound rules**

---

## 🔑 Key Pairs

- SSH public/private key pair used to login to EC2
- Generated once during instance launch or externally via AWS Console
- If you lose the private key, you cannot SSH into the instance

---

## 📍 Elastic IPs (EIP)

- **Static public IP** for your EC2 instance
- **1 free EIP per region**, but you pay if not associated
- Use case: When you need a persistent public IP across stops/reboots

---

## ⚖️ Placement Groups

Used for **instance placement strategy** across physical hardware.

| Type       | Behavior                                                                 |
|------------|--------------------------------------------------------------------------|
| Cluster    | Same rack for low latency / high throughput networking                   |
| Spread     | Separate hardware → Max availability                                     |
| Partition  | Spread across partitions (groups of racks) for fault isolation           |

---

## 📈 EC2 Monitoring

| Type              | Details                                     |
|-------------------|---------------------------------------------|
| Basic Monitoring  | Metrics every **5 minutes** (free)          |
| Detailed Monitoring | Metrics every **1 minute** (paid)         |
| Tools             | CloudWatch, CloudTrail, EC2 dashboard       |

---

## 💾 EC2 Storage Options

### 1. EBS – Elastic Block Store

- Network-attached storage for EC2
- Data **persists** after instance stop
- Types:
  - General Purpose (gp3/gp2)
  - Provisioned IOPS (io1/io2)
  - Throughput optimized (st1)
  - Cold HDD (sc1)

### 2. Instance Store

- Ephemeral local storage (physically attached)
- **Data lost** when instance stops or terminates
- Use for:
  - Buffer/cache
  - Temp data

---

## 🧱 AMI – Amazon Machine Image

- **Template** for launching EC2 with pre-installed OS/software
- Public or private
- Custom AMIs useful for:
  - Faster boot
  - Pre-installed dependencies
  - Security and consistency

---

## 🧾 EC2 Pricing Model

| Factor                | Examples                                                        |
|------------------------|-----------------------------------------------------------------|
| Instance type          | `t2.micro`, `m5.large`, etc.                                    |
| Purchase option        | On-Demand, Reserved, Spot, Dedicated                            |
| OS and Licensing       | Linux (cheaper), Windows, RHEL                                  |
| Region                 | Pricing varies by region                                        |
| Usage time             | Billed **per second**, minimum 60 seconds                       |
| Extra charges          | EBS, NAT Gateway, Elastic IPs (idle), data transfer             |

---

## 🛡️ Shared Responsibility Model for EC2

| AWS Manages                                 | You Manage                                    |
|---------------------------------------------|-----------------------------------------------|
| Physical hardware, network, hypervisor      | EC2 instance OS, updates, application logic   |
| Data center, storage infrastructure          | Data, security patches, firewall (SG/NACLs)   |
| Hardware security                            | IAM roles, SSH key rotation, monitoring       |

---

## 🧠 EC2 Key Exam Takeaways

- EC2 = IaaS virtual servers
- Choose right **instance type** and **purchase option**
- Use **Security Groups** and **Key Pairs** for security
- Leverage **User Data** to automate instance setup
- Store persistent data in **EBS**, not Instance Store
- **Elastic IPs** are static IPs, but chargeable when unassociated
- Understand **burstable T2/T3**, AMIs, placement groups
- Watch out for shared responsibility boundaries

---
