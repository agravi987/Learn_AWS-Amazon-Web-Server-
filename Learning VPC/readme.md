# 🌐 Learning VPC — Build Your Own Private Network on AWS

> **VPC (Virtual Private Cloud)** is your own private data center inside AWS. It gives you full control over networking — IP ranges, subnets, route tables, gateways, and firewalls.

---

## 🧠 Architecture Overview

```
VPC (10.0.0.0/16)
│
├── Public Subnet (10.0.1.0/24)
│   └── EC2 Instance (Web Server)
│
├── Internet Gateway
│
└── Route Table (0.0.0.0/0 → IGW)
```

> [!NOTE]
> No NAT Gateway is used in this lab — NAT costs money 💰

---

## 🛠 Step-by-Step Implementation

### ✅ Step 1 — Create VPC

1. Go to **VPC Dashboard → Create VPC**
2. Configure:

   | Setting   | Value         |
   | --------- | ------------- |
   | Name      | `My-VPC-Lab`  |
   | IPv4 CIDR | `10.0.0.0/16` |
   | Tenancy   | Default       |

3. Click **Create**

✔ You now own your private network 😎

---

### ✅ Step 2 — Create Public Subnet

1. Go to **VPC → Subnets → Create Subnet**
2. Configure:

   | Setting | Value           |
   | ------- | --------------- |
   | VPC     | `My-VPC-Lab`    |
   | Name    | `Public-Subnet` |
   | AZ      | `ap-south-1a`   |
   | CIDR    | `10.0.1.0/24`   |

3. After creating, go to **Edit subnet settings**
4. ✅ Enable **Auto-assign public IPv4**

> [!IMPORTANT]
> Enabling auto-assign public IP is essential — without it, instances won't get a public IP automatically.

---

### ✅ Step 3 — Create Internet Gateway (IGW)

1. Go to **VPC → Internet Gateways → Create**
2. Name: `My-IGW`
3. After creating → **Attach to VPC** → Select `My-VPC-Lab`

> [!WARNING]
> Without an IGW attached, your VPC has **no internet access** 🚫🌍

---

### ✅ Step 4 — Create Route Table

1. Go to **VPC → Route Tables → Create**

   | Setting | Value        |
   | ------- | ------------ |
   | Name    | `Public-RT`  |
   | VPC     | `My-VPC-Lab` |

2. **Add Route:**
   - Edit routes → Add route:
     - **Destination:** `0.0.0.0/0`
     - **Target:** Internet Gateway (`My-IGW`)
   - Save

3. **Associate with Subnet:**
   - Go to **Subnet Associations → Edit** → Select `Public-Subnet`

✔ Your subnet is now officially **public** 🌍🔥

---

### ✅ Step 5 — Launch EC2 in Public Subnet

1. Go to **Amazon EC2 → Launch Instance**
2. Configure:

   | Setting               | Value                      |
   | --------------------- | -------------------------- |
   | Name                  | `VPC-Test-Server`          |
   | AMI                   | Amazon Linux 2 (Free Tier) |
   | Instance Type         | `t2.micro` (Free Tier)     |
   | Key Pair              | Create or select existing  |
   | VPC                   | `My-VPC-Lab`               |
   | Subnet                | `Public-Subnet`            |
   | Auto-assign Public IP | Enable                     |

3. **Security Group Rules:**

   | Type | Port | Source      |
   | ---- | ---- | ----------- |
   | SSH  | 22   | My IP       |
   | HTTP | 80   | `0.0.0.0/0` |

4. Click **Launch** 🚀

---

### ✅ Step 6 — Install a Simple Web Server

Connect via SSH and run:

```bash
sudo yum update -y
sudo yum install httpd -y
sudo systemctl start httpd
sudo systemctl enable httpd
echo "Hello Ravi Cloud Engineer 🚀" | sudo tee /var/www/html/index.html
```

Open in browser:

```
http://<your-public-ip>
```

🔥 **BOOM** — You built a VPC manually like a real DevOps engineer.

---

## 🎓 What You Learned

| Component      | Why It's Important   |
| -------------- | -------------------- |
| CIDR           | IP range planning    |
| Subnet         | Network segmentation |
| IGW            | Internet access      |
| Route Table    | Traffic control      |
| Security Group | Firewall             |
| EC2            | Compute inside VPC   |

### 🔗 This is foundational for:

- Multi-tier architecture
- Load balancers
- NAT Gateways
- Kubernetes (EKS)
- Production systems

---

## 💰 Cleanup — Free Tier Safety

> [!CAUTION]
> Always clean up after labs to avoid unexpected AWS charges.

### 🔴 Step 1 — Terminate EC2

- **EC2 → Instances → Select → Terminate**
- Wait until fully terminated

### 🔴 Step 2 — Release Elastic IP _(if allocated)_

- **EC2 → Elastic IPs → Select → Release**

> ⚠ Elastic IPs cost money if **not attached** to a running instance.

### 🔴 Step 3 — Delete Route Table

1. Disassociate the subnet first
2. Delete the custom route table

> The default route table cannot be deleted.

### 🔴 Step 4 — Detach & Delete IGW

1. **VPC → Internet Gateway** → Detach from VPC
2. Then **Delete**

### 🔴 Step 5 — Delete Subnet

- Delete `Public-Subnet`

### 🔴 Step 6 — Delete VPC

- Finally delete `My-VPC-Lab`

✅ All resources cleaned up — **Free Tier safe!**

---

> **Author:** Ravi — Learning AWS one service at a time ☁️🚀
