# 🌐 Amazon VPC – AWS Developer & Associate Exam Notes

---

## 🧠 What is a VPC?

**VPC (Virtual Private Cloud)** is your **own isolated private network** in AWS where you can **launch AWS resources** (like EC2, RDS, Lambda, etc.).

- Think of it as your **own data center in the cloud**.
- It's **region-scoped**, but you can span across **multiple Availability Zones (AZs)** using subnets.

---

## 📦 Key VPC Concepts

| Concept           | Description |
|------------------|-------------|
| **VPC**          | A virtual network that you define — customizable IP ranges, subnets, route tables, gateways, etc. |
| **CIDR block**   | IP range for your VPC (e.g., `10.0.0.0/16`) |
| **Subnets**      | Subdivision of a VPC tied to an **AZ**. Can be **Public** or **Private**. |
| **AZ (Availability Zone)** | Physically isolated zone within a region. |
| **Route Tables** | Controls network traffic routing within subnets. |
| **IGW (Internet Gateway)** | Allows public internet access to public subnets. |
| **NAT Gateway / NAT Instance** | Allow **private subnet** instances to access the internet **without being exposed**. |

---

## 🧭 Public vs Private Subnet

| Feature               | Public Subnet                         | Private Subnet                      |
|----------------------|----------------------------------------|-------------------------------------|
| Internet Access       | ✅ via IGW                             | ✅ via NAT Gateway/Instance         |
| IGW Route             | Must have route to Internet Gateway   | No direct route to Internet Gateway |
| Use Case              | Load balancers, bastion hosts, etc.   | App servers, databases, etc.        |

> 💡 Subnets are AZ-specific. Use **multiple subnets across AZs** for high availability.

---

## 🌐 Internet Gateway (IGW)

- Attached to a VPC to enable public internet access.
- Public subnets route traffic to IGW.
- **Stateless** (doesn’t remember request/response).

---

## 🔁 NAT Gateway vs NAT Instance

| Feature           | NAT Gateway (Recommended)               | NAT Instance                     |
|------------------|------------------------------------------|----------------------------------|
| Managed by AWS   | ✅                                       | ❌ (You manage it)               |
| Availability     | Highly available in AZ                   | Needs to be manually scaled      |
| Performance      | Scalable                                | Bottlenecks possible             |
| Cost             | More expensive                          | Cheaper                          |

> 💡 Use **NAT Gateway** for production, **NAT Instance** only for cost-limited dev/test.

---

## 🔐 Security in VPC

### ✅ Security Groups (SG)
- Acts as a **virtual firewall** at the **ENI/instance level**.
- **Stateful**: return traffic is automatically allowed.
- Only **ALLOW** rules.
- Can reference **other SGs** or IPs.

### ✅ Network ACLs (NACL)
- Firewall at the **subnet level**.
- **Stateless**: response traffic must be explicitly allowed.
- Can have **ALLOW and DENY** rules.
- Evaluated in **order**, numbered rules.

| Feature           | Security Groups                | Network ACLs                    |
|------------------|----------------------------------|----------------------------------|
| Level             | ENI (instance)                 | Subnet                          |
| Stateful?         | ✅                             | ❌                              |
| Rules Type        | Only ALLOW                    | ALLOW & DENY                   |
| Rule Evaluation   | All rules applied             | In number order                |
| Use Case          | EC2, Lambda, ENI protection   | Subnet-wide control            |

---

## 🛠️ VPC Flow Logs

- Capture IP traffic for **VPC, Subnets, or ENIs**.
- Use to **monitor traffic, troubleshoot connectivity**, and **audit**.
- Data can be sent to **CloudWatch Logs**, **S3**, or **Kinesis**.
- Supports AWS-managed services like **ELB, RDS, Aurora, ElastiCache**.

> 💡 Doesn’t capture DNS or Windows license activation traffic.

---

## 🔗 VPC Peering

- Connects two VPCs **privately using AWS’s backbone**.
- VPCs act as if on the same network.
- Must have **non-overlapping CIDR blocks**.
- **Not transitive**: A↔B and B↔C ≠ A↔C.

> 💡 Manually update route tables and SGs to allow traffic between VPCs.

---

## 🔌 VPC Endpoints

Used to connect **privately to AWS services** (without going through the internet):

| Type                    | Use Case                         | Example Services              |
|-------------------------|----------------------------------|-------------------------------|
| **Interface Endpoint**  | Uses ENI in subnet (PrivateLink) | S3, SSM, EC2 API, SecretsMgr  |
| **Gateway Endpoint**    | Targets S3 & DynamoDB only       | S3, DynamoDB                  |

> 💡 Improve security by avoiding public traffic. Works only **within your VPC**.

---

## 🛰️ Site-to-Site VPN

- Connects **on-premises network to AWS** VPC over **internet**.
- Encrypted using IPsec.
- Quick to set up, **public** internet route.

---

## 🧬 AWS Direct Connect (DX)

- Dedicated **private connection** between on-premises and AWS.
- Offers **low latency and higher bandwidth**.
- Takes ~**1 month** to provision.
- Not encrypted (can combine with VPN).

---

## 🔐 VPC Endpoint vs VPN vs Direct Connect

| Feature         | VPC Endpoint          | VPN                        | Direct Connect                |
|----------------|------------------------|----------------------------|-------------------------------|
| Target          | AWS Services only      | On-prem ↔ AWS              | On-prem ↔ AWS                |
| Route Type      | Private AWS Network    | Over public internet       | Over private line            |
| Latency         | Low                    | Moderate                   | Very Low                     |
| Encryption      | N/A                    | Encrypted (IPsec)          | Not encrypted by default     |
| Setup Time      | Minutes                | Hours                      | Weeks                        |

---

## 🧱 Typical 3-Tier VPC Architecture

