## 🧱 Scalability & High Availability

### 🔄 Scalability

* Ability of a system to handle a growing amount of work or traffic.
* **Two types**:

  * **Vertical Scalability**: Increasing instance size (e.g., t2.micro → t2.large). Used in non-distributed systems like RDS or ElastiCache. Limited by hardware. 
   <img width="200" height="200" alt="image" src="https://github.com/user-attachments/assets/e4592d14-4fa4-45d3-9023-bcbdda99baff" />

  * **Horizontal Scalability**: Increasing number of instances (scale-out). Common for distributed systems (e.g., EC2 Auto Scaling).
      <img width="200" height="300" alt="image" src="https://github.com/user-attachments/assets/cd0c485e-95b9-4fc7-a777-6c2b5a273d41" />


### 🔒 High Availability (HA)

* Ensures system runs in multiple **Availability Zones (AZs)** to survive a data center failure.
* Typically implemented using **Multi-AZ deployments** or **Auto Scaling Groups**.
* HA setups:

  * **Passive**: e.g., RDS Multi-AZ.
  * **Active**: e.g., Web servers behind a Load Balancer.
 <img width="200" height="400" alt="image" src="https://github.com/user-attachments/assets/8c8d922b-9765-482d-a5aa-96f623ec28bd" />


---

## ⚙️ EC2: Scalability & High Availability

* **Vertical Scaling**: t2.nano (0.5 GB RAM) → u-12tb1.metal (12 TB RAM).
* **Horizontal Scaling**:

  * Auto Scaling Groups (ASG)
  * Load Balancers (LB)
* **High Availability**:

  * Run instances across multiple AZs using ASG + LB.

---

## ⚖️ Load Balancing

### 🔁 What is Load Balancing?

* Distributes traffic to multiple downstream servers.
* Improves reliability, availability, and performance.
  <img width="1000" height="500" alt="image" src="https://github.com/user-attachments/assets/b60571d0-2cdb-44c2-9e57-6e56a09ee915" />


### 🎯 Benefits of Load Balancers

* Distribute load evenly
* Single DNS entry point
* Health checks
* SSL termination
* Stickiness via cookies
* HA across AZs
* Separate public/private traffic

### 🧰 AWS Elastic Load Balancer (ELB)

* **Managed** solution by AWS.
* Integrated with EC2, ASG, ECS, ACM, CloudWatch, Route 53, etc.
* AWS handles:

  * Fault tolerance
  * Maintenance
  * Upgrades

---

## ✅ Health Checks

* Load Balancers monitor instance health via:

  * Protocol: HTTP/TCP
  * Port
  * Path (e.g., `/health`)
* Mark instances as unhealthy if they don't respond with HTTP 200.

---

## 📦 Types of Load Balancers

| Load Balancer | Layer | Protocols              | Use Cases                                |
| ------------- | ----- | ---------------------- | ---------------------------------------- |
| **CLB**       | 4/7   | HTTP, HTTPS, TCP, SSL  | Legacy applications                      |
| **ALB**       | 7     | HTTP, HTTPS, WebSocket | Web & container-based apps               |
| **NLB**       | 4     | TCP, TLS, UDP          | High-performance, low-latency apps       |
| **GWLB**      | 3     | IP                     | Security appliances (firewalls, IDS/IPS) |

---

## 🔐 Load Balancer Security Groups

* **LB SG**: Allows inbound HTTP/HTTPS from internet.
* **App SG**: Allows traffic only from LB.

---

## 🌐 Application Load Balancer (ALB)

* Layer 7 (HTTP/S)

* Supports:

  * Host/path-based routing
  * Redirects
  * WebSocket, HTTP/2
  * Query String, Header-based routing
  * ECS dynamic port mapping
  * Lambda targets

* **Target Types**:

  * EC2, ECS tasks, Lambda, IP addresses

* **X-Forwarded Headers**:

  * `X-Forwarded-For`: Client IP
  * `X-Forwarded-Proto`: Protocol
  * `X-Forwarded-Port`: Port
 
<img width="1000" height="500" alt="image" src="https://github.com/user-attachments/assets/3867b40f-ab14-4c56-82ce-0d71b0261563" />
<img width="1000" height="500" alt="image" src="https://github.com/user-attachments/assets/f718f5d4-40d6-4391-bb3e-b6e9bf26eff7" />



---

## ⚡ Network Load Balancer (NLB)

* Layer 4
* TCP, TLS, UDP
* Millions of req/sec
* Ultra-low latency
* **Static IPs per AZ**, support for Elastic IP
* **Used for**:

  * High performance
  * Whitelisting IPs
  * Legacy systems

---

## 🚪 Gateway Load Balancer (GWLB)

* Layer 3 (Network Layer)
* Deploy 3rd-party virtual appliances like firewalls
* Combines:

  * Transparent Gateway
  * Load Balancer
* Uses **GENEVE** protocol (port 6081)

---

## 🍪 Sticky Sessions (Session Affinity)

* Same client → same backend instance.
* Use **cookies**:

  * Custom App Cookie
  * Application Cookie (ALB: `AWSALBAPP`)
  * Duration Cookie (`AWSALB`, `AWSELB`)

⚠️ Trade-off: Can create load imbalance.

<img width="200" height="300" alt="image" src="https://github.com/user-attachments/assets/de5e70cf-960c-4510-a683-1e9de40009cd" />


---

## 🌍 Cross-Zone Load Balancing

| Load Balancer | Default  | Inter-AZ Data Charges |
| ------------- | -------- | --------------------- |
| **ALB**       | Enabled  | ❌ No charges          |
| **NLB**       | Disabled | ✅ Charges if enabled  |
| **CLB**       | Disabled | ❌ No charges          |

---

## 🔒 SSL/TLS Basics

* Encrypts in-transit traffic between client and load balancer.
* **Certificates**:

  * Managed via **ACM**
  * Use **X.509 certs**
* **SNI (Server Name Indication)**:

  * Supports multiple SSL certs
  * Works with ALB, NLB, CloudFront
  * Not supported by CLB

---

## 🔄 Connection Draining / Deregistration Delay

* Allows in-flight requests to complete before instance deregisters.
* Set between 1 – 3600 seconds (default: 300s).
* Can be disabled (set to 0).
* Names:

  * **CLB**: Connection Draining
  * **ALB/NLB**: Deregistration Delay
