# 🌐 Amazon Route 53 Documentation

Amazon Route 53 is a highly available and scalable Domain Name System (DNS) web service. It is designed to give developers and businesses an extremely reliable and cost-effective way to route end users to Internet applications.

---

## 📑 Table of Contents

1. [Required Basic Knowledge](#1-required-basic-knowledge)
2. [Registering a Domain Name](#2-registering-a-domain-name)
3. [Hosted Zone Configuration](#3-hosted-zone-configuration)
4. [Creating DNS Record (Example: EC2 Website)](#4-creating-dns-record-example-ec2-website)
5. [Configuring Health Check](#5-configuring-health-check)
6. [Validating Health Check](#6-validating-health-check)
7. [Real Production Example](#7-real-production-example)
8. [Important Best Practices](#8-important-best-practices)

---

## 1️⃣ Required Basic Knowledge

Before working with Route 53, you must understand these core concepts:

### 🔹 1. DNS Fundamentals

| Concept                | Meaning                                                     |
| :--------------------- | :---------------------------------------------------------- |
| **DNS**                | Converts domain names (e.g., `example.com`) → IP addresses. |
| **Port**               | DNS primarily uses **Port 53**.                             |
| **TTL**                | Time-To-Live; how long a DNS record is cached.              |
| **Recursive Resolver** | The server that queries DNS servers to find the IP.         |
| **Authoritative DNS**  | The final source of truth for a domain's records.           |

### 🔹 2. Networking Basics

- **IPv4 vs IPv6**: Standard vs. next-generation IP addresses.
- **Public vs Private IP**: Accessible from the internet vs. internal network only.
- **VPC**: Virtual Private Cloud (isolated network).
- **Load Balancer**: Distributes traffic across servers.
- **High Availability**: Designing for minimal downtime.

### 🔹 3. AWS Basic Knowledge

- **AWS Regions**: Geographic locations of AWS infrastructure.
- **IAM Roles & Permissions**: Controlling access to Route 53.
- **EC2 Basics**: Virtual servers in the cloud.
- **Elastic Load Balancer (ELB)**: Automated traffic distribution.

---

## 2️⃣ Registering a Domain Name

Route 53 allows you to register domain names directly within the AWS console.

### 📌 Steps to Register Domain

1.  **Login** to the AWS Console.
2.  Navigate to **Route 53**.
3.  Click **"Registered Domains"** → **"Register Domain"**.
4.  Enter your desired domain (e.g., `example.com`) and check availability.
5.  Choose registration period (1–10 years).
6.  Enter contact details and complete payment.

> [!NOTE]
> After purchase, Route 53 automatically creates a **Public Hosted Zone** for you. NS (Name Server) and SOA (Start of Authority) records are generated automatically.

### 🧠 What Happens Internally?

- AWS becomes your **Domain Registrar**.
- Standard Name Servers are assigned.
- The domain is officially registered in the global registry.

---

## 3️⃣ Hosted Zone Configuration

A hosted zone is a container that stores DNS records for a domain.

| Type                    | Use Case                       |
| :---------------------- | :----------------------------- |
| **Public Hosted Zone**  | For internet-facing domains.   |
| **Private Hosted Zone** | For internal DNS within a VPC. |

### 📌 Creating a Hosted Zone manually

If the domain was purchased outside of AWS:

1.  Go to **Route 53** → **"Create Hosted Zone"**.
2.  Enter the Domain Name.
3.  Select **Public Hosted Zone**.
4.  Copy the generated **NS records** and update them at your external domain registrar.

---

## 4️⃣ Creating DNS Record (Example: EC2 Website)

To point a domain to an EC2 instance:

- **EC2 Public IP:** `3.110.45.20`

### Steps:

1.  Open your **Hosted Zone**.
2.  Click **"Create Record"**.
3.  **Record Type:** `A` (IPv4 address).
4.  **Value:** Enter the EC2 Public IP.
5.  **TTL:** `300` seconds (standard).
6.  Save Record.

> [!TIP]
> Now, `example.com` will resolve to `3.110.45.20`.

---

## 5️⃣ Configuring Health Check

Route 53 can monitor the health of your endpoints and route traffic accordingly.

### 🔎 Health Check Types

| Type                 | Monitors                               |
| :------------------- | :------------------------------------- |
| **HTTP/HTTPS**       | Website availability and status codes. |
| **TCP**              | Connection at the port level.          |
| **CloudWatch Alarm** | Health based on custom metrics/alarms. |

### 📌 Steps to Create Health Check

1.  Go to **Route 53** → **Health Checks** → **Create Health Check**.
2.  Choose **Endpoint** as the monitoring target.
3.  Specify Protocol (**HTTP/HTTPS/TCP**), IP/Domain, and Port.
4.  Set **Failure Threshold** (e.g., 3 consecutive failures before marking unhealthy).

### 🔁 Failover Setup (High Availability)

1.  In your Hosted Zone, create a record with **Routing Policy: Failover**.
2.  Set the **Primary** record and attach your health check.
3.  Create a **Secondary** record pointing to a backup server.

---

## 6️⃣ Validating Health Check

### 🔹 Method 1: AWS Console

Monitor the status in the Route 53 dashboard. Look for **Healthy / Unhealthy** status.

### 🔹 Method 2: Manual Failure Testing

Stop your web server to simulate a failure:

```bash
sudo systemctl stop nginx
```

Wait 1–2 minutes and check if the health status turns **Unhealthy**.

### 🔹 Method 3: CLI Tools

```bash
nslookup example.com
# OR
dig example.com
```

Observe if the resolved IP changes to the secondary server during failover.

---

## 7️⃣ Real Production Example

- **Primary Server:** Mumbai Region
- **Secondary Server:** Singapore Region
- **Routing Policy:** Failover (with health checks)

**Scenario:** If the Mumbai server fails, Route 53 detects the failure and automatically routes all traffic to Singapore.

- **Benefit:** High Availability, Disaster Recovery, and Business Continuity.

---

## 8️⃣ Important Best Practices

> [!IMPORTANT]
>
> - **Low TTL:** Use a low TTL (e.g., 60s) for failover records to ensure fast propagation.
> - **Primary Health Checks:** Always attach a health check to the primary resource.
> - **Alias Records:** Use Alias records instead of CNAME for AWS resources like Load Balancers.
> - **CloudWatch:** Enable CloudWatch monitoring for real-time alerts.
> - **Test Regularly:** Periodically test your failover setup.
