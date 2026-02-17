# 📘 Deploying Dockerized Node App on EC2 with RDS (MySQL)

This guide documents the process of deploying a Node.js application in a Docker container on an EC2 instance, connecting to an Amazon RDS MySQL database.

## 1️⃣ Architecture Overview

**Flow:** `User` → `EC2 (Docker Container)` → `RDS (MySQL)`

### Services Used

- **Amazon Web Services**
  - Amazon EC2
  - Amazon RDS
- **Docker**
- **MySQL 8**

---

## 2️⃣ Network Planning (Very Important ⚠️)

Before creating any resources, ensure the following network configurations:

- [x] **Use the Same VPC** for both EC2 and RDS.
- [x] **Configure Security Groups (SG):**
  - Allow **Port 3306** (MySQL) only from the **EC2 Security Group**.
  - Allow **Port 80** (HTTP) from the **Internet (0.0.0.0/0)**.

---

## 3️⃣ Step 1 — Create RDS (MySQL)

### Configuration Details

| Setting           | Value                                |
| :---------------- | :----------------------------------- |
| **Engine**        | MySQL 8                              |
| **Template**      | Free tier                            |
| **DB Identifier** | `node-mysql-db`                      |
| **Username**      | `admin`                              |
| **Password**      | _(Use a strong password)_            |
| **VPC**           | Default (or your custom VPC)         |
| **Public Access** | **YES** (For learning purposes only) |

### 🔐 RDS Security Group Setup (Critical)

Create a new Security Group named `rds-sg`.

**Inbound Rules:**
| Type | Port | Source | Notes |
| :--- | :--- | :--- | :--- |
| MySQL/Aurora | 3306 | **EC2 Security Group** | ❌ DO NOT use `0.0.0.0/0`. ✔ Always allow only EC2 SG. |

### 📍 Get RDS Endpoint

1. Go to **RDS** → **Databases** → `node-mysql-db` → **Connectivity**.
2. Copy the **Endpoint** (e.g., `node-mysql-db.xxxxxx.us-east-1.rds.amazonaws.com`).
3. Note the **Port** (usually `3306`).

> **Note:** This endpoint will be your `DB_HOST`.

---

## 4️⃣ Step 2 — Create EC2 Instance

### Configuration Details

| Setting                   | Value          |
| :------------------------ | :------------- |
| **AMI**                   | Amazon Linux 2 |
| **Instance Type**         | t2.micro       |
| **VPC**                   | Same as RDS    |
| **Auto-assign Public IP** | Enabled        |

### 🔐 EC2 Security Group Setup

Create a new Security Group named `ec2-node-sg`.

**Inbound Rules:**
| Type | Port | Source | Notes |
| :--- | :--- | :--- | :--- |
| SSH | 22 | Your IP | For management access |
| HTTP | 80 | `0.0.0.0/0` | For web access |

> **Note:** There is no need to open Port 3306 on the EC2 Security Group.

---

## 5️⃣ Step 3 — Install Docker on EC2

SSH into your EC2 instance and run the following commands:

```bash
# Update and install Docker
sudo yum update -y
sudo yum install -y docker

# Start the Docker service
sudo service docker start

# Add ec2-user to the docker group (to run docker without sudo)
sudo usermod -aG docker ec2-user
```

> **Action Required:** Log out and log back in for the group changes to take effect.

---

## 6️⃣ Step 4 — Pull Application Image

Pull the Docker image for the application:

```bash
docker pull philippaul/node-mysql-app:02
```

---

## 7️⃣ Step 5 — Run Application Container

Run the application container with the necessary environment variables to connect to your RDS instance.

```bash
docker run -d -p 80:3000 \
  -e DB_HOST="your-rds-endpoint" \
  -e DB_USER="admin" \
  -e DB_PASSWORD="your-password" \
  --name node-app \
  philippaul/node-mysql-app:02
```

---

## 8️⃣ Step 6 — Verify Database Connectivity (Manual Test)

You can verify the connection manually by running a temporary MySQL container:

```bash
docker run -it --rm mysql:8.0 mysql -h your-rds-endpoint -u admin -p
```

If successful, you will see the MySQL prompt:

```sql
mysql>
```

---

## 9️⃣ Debugging Guide

### ❌ Error: `ETIMEDOUT`

- **Cause:**
  - Wrong Security Group configuration.
  - EC2 and RDS are in different VPCs.
  - Port 3306 is blocked.
- **Fix:**
  - Ensure the RDS Security Group allows traffic from the EC2 Security Group.
  - Verify both resources are in the same VPC.
  - Check if the RDS status is `Available`.

### ❌ Error: `Access Denied`

- **Cause:** Wrong username or password.
- **Fix:** Verify credentials in the RDS configuration settings.

### ❌ Error: Container Stops Immediately

- **Check Logs:**
  ```bash
  docker logs node-app
  ```

---

## 🔟 Validation Checklist ✅

Before running the container, confirm the following:

- [ ] **EC2 and RDS** are in the same VPC.
- [ ] **RDS Security Group** allows inbound traffic from the **EC2 Security Group**.
- [ ] **Port 80** is open on the EC2 Security Group.
- [ ] You have the correct **RDS Endpoint**.
- [ ] You have the correct **DB Credentials**.
- [ ] **RDS Status** is showing as `Available`.

---

## 🔒 Security Best Practices (Production)

For a production environment, consider the following improvements:

1.  **Secrets Management:** Store database passwords in **AWS Secrets Manager**, never in plain text commands.
2.  **Private Access:** Disable **Public Access** on the RDS instance.
3.  **Network Isolation:** Place the RDS instance in a **Private Subnet**.
4.  **Load Balancing:** Use an **Application Load Balancer (ALB)** in front of your EC2 instances.
5.  **High Availability:** Enable **Multi-AZ** deployment for RDS.
