# Amazon Route 53 – Detailed Notes (AWS Exam POV)

## 🌐 What is DNS?

**DNS (Domain Name System)** is like the phonebook of the internet. It translates **human-readable domain names** (e.g., `www.google.com`) into **IP addresses** (e.g., `172.217.18.36`) so computers can communicate with each other.

### 🧠 Key Concepts

* **Human-friendly:** Users type names like `example.com`, not IPs.
* **Backbone of the Internet:** Enables websites and services to be found.
* **Hierarchical Naming Structure:**

  * `.com` (Top Level Domain)
  * `example.com` (Second Level Domain)
  * `www.example.com` (Subdomain)
  * `api.www.example.com` (Fully Qualified Domain Name - FQDN)

## 🏷️ DNS Terminologies

| Term                 | Description                                                                                         |
| -------------------- | --------------------------------------------------------------------------------------------------- |
| **Domain Registrar** | Where you purchase domain names. (e.g., GoDaddy, Route 53)                                          |
| **DNS Records**      | Define how domain names map to IP addresses or services.                                            |
| **Zone File**        | Contains DNS records for a domain.                                                                  |
| **Name Server (NS)** | Servers that answer DNS queries. Can be authoritative (final answer) or non-authoritative (cached). |
| **TLD**              | Top-Level Domain like `.com`, `.org`, `.in`.                                                        |
| **SLD**              | Second-Level Domain like `google.com`, `amazon.in`.                                                 |
| **Subdomain**        | A domain that is part of a larger domain (`api.example.com`).                                       |
| **FQDN**             | Fully Qualified Domain Name (e.g., `app.prod.example.com.`)                                         |

## ⚙️ How DNS Works

1. User types `example.com` into browser.
2. Browser asks local DNS server (provided by ISP).
3. If unknown, query goes to **Root DNS Server** (managed by IANA).
4. Then to **TLD Server** (e.g., `.com`).
5. Then to **Authoritative Name Server** (e.g., hosted in Route 53).
6. Gets IP address and contacts the web server.

---

## 🧭 Amazon Route 53 Overview

### ✅ What is Route 53?

* **Highly available, scalable, and fully managed DNS service** by AWS.
* Also acts as a **Domain Registrar**.
* **Authoritative DNS**: You control the DNS records.
* Supports **health checks** and **DNS-based routing policies**.
* Named "53" because DNS uses **port 53**.

---

## 📄 DNS Record Types

DNS Records determine how to route traffic. Each record includes:

* **Name**: Domain or subdomain (e.g., `api.example.com`)
* **Type**: Record type (see below)
* **Value**: IP or another name
* **TTL (Time to Live)**: Cache time at DNS resolvers
* **Routing Policy**: How to respond to DNS queries

### 🔥 Must-Know Record Types

| Type      | Description                                                                           |
| --------- | ------------------------------------------------------------------------------------- |
| **A**     | Maps hostname to **IPv4** address                                                     |
| **AAAA**  | Maps hostname to **IPv6** address                                                     |
| **CNAME** | Maps hostname to **another hostname** (e.g., `app.example.com` → `elb.amazonaws.com`) |
| **NS**    | Lists the **Name Servers** for a zone                                                 |

### 🧠 Advanced Record Types

| Type    | Description                                   |
| ------- | --------------------------------------------- |
| **MX**  | Mail server records                           |
| **TXT** | Text records (SPF, domain verification, etc.) |
| **SOA** | Start of Authority (Zone metadata)            |
| **SRV** | Service locator records                       |
| **CAA** | Cert Authority Authorization                  |
| **PTR** | Reverse DNS                                   |
| **SPF** | Sender Policy Framework (deprecated, use TXT) |

---

## 🧰 Hosted Zones

* A **Hosted Zone** is a container for DNS records for a domain.

### 🌐 Types:

* **Public Hosted Zone**: Routes traffic on the **internet** (e.g., `example.com`).
* **Private Hosted Zone**: Routes traffic **within a VPC** (e.g., `db.internal.company`).

### 💰 Pricing:

* \$0.50 per hosted zone per month

---

## 🧪 TTL (Time To Live)

* TTL determines how long a record is cached.

| TTL Value           | Impact                                        |
| ------------------- | --------------------------------------------- |
| High (e.g., 24 hrs) | Lower cost, but records update slowly         |
| Low (e.g., 60 sec)  | Fast updates, but higher DNS traffic and cost |

* **Alias records** do **not** allow manual TTL; they follow target TTL automatically.

---

## 🔁 CNAME vs Alias

| Feature               | CNAME             | Alias                                |
| --------------------- | ----------------- | ------------------------------------ |
| Points to             | Another hostname  | AWS resource (e.g., ALB, CloudFront) |
| Works at root domain? | ❌                 | ✅                                    |
| TTL configurable?     | ✅                 | ❌                                    |
| Cost                  | Normal query cost | **Free**                             |

> 🔹 Use Alias for AWS resources like S3 websites, CloudFront, ELBs, etc.

---

## 🚦 Routing Policies in Route 53

Routing policies define **how Route 53 responds** to DNS queries. Route 53 **does not route** actual traffic.

### 1. **Simple Routing**

* Single resource or multiple IPs.
* No health checks.
* Random IP chosen if multiple are returned.

### 2. **Weighted Routing**

* Split traffic by percentage (e.g., 70% → A, 30% → B).
* Supports **health checks**.
* Use case: testing new app versions, regional distribution.

### 3. **Latency-based Routing**

* Sends traffic to resource with **lowest latency** (based on user’s location).
* Use case: Global apps where speed matters.
* Supports **health checks**.

### 4. **Failover Routing**

* **Primary (Active)** and **Secondary (Passive)** setup.
* Secondary used only if primary fails.
* Requires **health check** on the primary.

### 5. **Geolocation Routing**

* Based on **user’s geographical location** (continent, country, or state).
* Create a **default** rule as fallback.
* Use case: content restriction, localization.

### 6. **Geoproximity Routing** (Requires Traffic Flow)

* Similar to geolocation but more flexible.
* Use **bias values** to shift more traffic to a region.
* Supports AWS & non-AWS resources.

### 7. **IP-based Routing**

* Route based on user **IP CIDR** ranges.
* Useful for ISP-specific optimization.

### 8. **Multi-value Answer Routing**

* Return multiple healthy endpoints (up to 8).
* Can have **health checks**.
* Not a replacement for load balancer.

---

## 🩺 Health Checks

Used to determine **resource availability**.

### 3 Types of Health Checks:

1. **Endpoint Monitoring**

   * HTTP/S or TCP
   * Must respond with `2xx` or `3xx`
   * Can check for specific text in body
2. **Calculated Health Checks**

   * Combines multiple health checks using `AND`, `OR`, `NOT`
   * Monitor **maintenance windows** or composite conditions
3. **CloudWatch Alarm Health Check**

   * Useful for **private resources**
   * Monitor based on CW alarms (e.g., RDS, Lambda, CPU usage)

> Health checkers are **external** to VPC. For internal checks, use CloudWatch alarm.

---

## 🗺️ Domain Registrar vs DNS Service

| Domain Registrar                 | DNS Service                            |
| -------------------------------- | -------------------------------------- |
| Buys domain name (e.g., GoDaddy) | Hosts DNS records (e.g., Route 53)     |
| Provides basic DNS               | Can be changed to another DNS provider |
| One-time or annual fee           | Ongoing fee per hosted zone            |

> You can buy a domain from GoDaddy and use Route 53 as the DNS provider by updating NS records.

---

## 🧪 Tips for the AWS Exam

✅ Know when to use:

* **Alias vs CNAME**
* **Public vs Private Hosted Zones**
* **Health Checks types**
* **Routing policies & their use cases**
* **TTL impact on caching**
* **Geolocation vs Geoproximity vs Latency-based Routing**
* **Multi-Value Routing ≠ ELB**
