# 📘 Deploying NGINX on Amazon Elastic Container Service (ECS)

## 1️⃣ Project Overview

This project demonstrates **containerized application deployment** using **Amazon ECS**. A simple NGINX web server was deployed using the official Docker image:

```
nginx:latest
```

The deployment includes:

- **ECS Cluster** — Logical grouping of compute resources
- **Task Definition** — Blueprint for the container
- **ECS Service** — Maintains desired running tasks
- **Public Access** — Via Application Load Balancer (ALB)

---

## 2️⃣ Architecture Overview 🏗️

### Core ECS Components

| Component           | Purpose                                   |
| ------------------- | ----------------------------------------- |
| **Cluster**         | Logical grouping of compute resources     |
| **Task Definition** | Blueprint of the container                |
| **Task**            | Running instance of the container         |
| **Service**         | Maintains desired number of running tasks |

---

## 3️⃣ What is Each ECS Component?

### 🔹 1. Cluster

An **ECS Cluster** is a logical boundary where tasks and services run.

It can use:

- **EC2 launch type** — You manage the underlying instances
- **Fargate launch type** — Fully serverless (AWS manages infrastructure)

> **In this project:** Launch type used — _Fargate / EC2_ (update based on your setup)

---

### 🔹 2. Task Definition

A **Task Definition** is like a Docker Compose blueprint. It defines:

- **Container image** → `nginx:latest`
- **CPU & Memory** allocation
- **Port mappings** → Container port `80` → Host/ALB port `80`
- **Network mode**
- **IAM role** (if needed)

#### Example Configuration

| Parameter  | Value          |
| ---------- | -------------- |
| **Image**  | `nginx:latest` |
| **CPU**    | 256            |
| **Memory** | 512 MB         |
| **Port**   | 80             |

---

### 🔹 3. Task

A **Task** is a running instance of the Task Definition.

When started:

1. ECS **pulls the image** from Docker Hub
2. **Container starts** running
3. NGINX serves the **default welcome page**

---

### 🔹 4. Service

An **ECS Service** ensures:

- ✅ Desired number of tasks are **always running**
- 🔄 **Auto-restart** if a task fails
- ⚖️ Integration with **Load Balancer**

> **Example:** Desired Count = `1` — If the task crashes, ECS automatically restarts it.

---

## 4️⃣ Deployment Steps

### Step 1 — Create ECS Cluster

Create a new cluster from the **ECS Console**.

### Step 2 — Create Task Definition

- Use `nginx:latest` as the container image
- Configure **CPU / Memory** (e.g., 256 CPU, 512 MB Memory)
- Map **port 80**

### Step 3 — Create Service

- Select the **cluster** created in Step 1
- Select the **task definition** created in Step 2
- Choose the **launch type** (Fargate or EC2)

### Step 4 — Configure Networking

- **VPC** — Default or custom
- **Subnets** — Public subnets
- **Security Group** — Allow inbound HTTP on port `80`

### Step 5 — (Optional) Attach Load Balancer

- Attach an **Application Load Balancer (ALB)** for production-grade access

### Step 6 — Access the Application

- Use the **Public IP** (EC2) or **ALB DNS name** (Fargate) to access NGINX

---

## 5️⃣ Networking Configuration 🌐

| Setting            | Value                   |
| ------------------ | ----------------------- |
| **VPC**            | Default / Custom        |
| **Subnets**        | Public                  |
| **Security Group** | Allow HTTP on port `80` |

**Inbound Rule:**

| Type | Port | Source      |
| ---- | ---- | ----------- |
| HTTP | 80   | `0.0.0.0/0` |

> ⚠️ This allows **public internet access** to the container.

---

## 6️⃣ Outcome ✅

After successful deployment:

- 🌐 **NGINX welcome page** is accessible via:
  - **Public IP** (if using EC2 launch type)
  - **ALB DNS name** (recommended for Fargate)

- ✅ This confirms:
  - **ECS orchestration** is working correctly
  - **Container networking** is properly configured
  - **Security group** rules are applied correctly
