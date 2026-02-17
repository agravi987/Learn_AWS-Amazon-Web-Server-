# 📘 Dockerized Node.js App with DynamoDB on EC2

## 1️⃣ Project Overview

This project demonstrates how to deploy a **Dockerized Node.js application** on an **AWS EC2 instance** that interacts with **Amazon DynamoDB** for NoSQL data storage.

### 🏗 Architecture

The system architecture consists of the following components:

| Component      | Purpose                                          |
| -------------- | ------------------------------------------------ |
| **Amazon EC2** | Hosts the application environment                |
| **Docker**     | Containerizes the Node.js application            |
| **DynamoDB**   | Stores contact data (NoSQL Database)             |
| **IAM**        | Manages secure authentication and access control |

**Flow:** `User` → `EC2 (Docker Container)` → `DynamoDB`

---

## 2️⃣ Prerequisites

Before starting, ensure you have the following:

- An active **AWS Account**
- An **EC2 Instance** (Amazon Linux 2 recommended)
- A **DynamoDB Table** created
- An **IAM User** with programmatic access (or IAM Role for EC2)
- **Security Group** configured to allow traffic on **Port 80 (HTTP)** and **Port 22 (SSH)**

---

## 3️⃣ Implementation Guide

### Step 1: Create DynamoDB Table

1. Go to the **DynamoDB Dashboard**.
2. Click **Create Table**.
3. Use the following settings:
   - **Table Name:** `Contacts`
   - **Partition Key (Primary Key):** `id` (Type: `String`)
4. Click **Create Table**.

> **Note:** We use `String` for the ID because contact IDs act as unique identifiers (UUIDs).

### Step 2: Launch EC2 Instance

1. Go to **EC2 Dashboard** and launch a new instance.
2. Select **Amazon Linux 2023** or **Amazon Linux 2**.
3. Choose instance type **t2.micro** (Free Tier eligible).
4. Configure **Security Group** to allow:
   - **SSH (Port 22)** – For connecting to the instance.
   - **HTTP (Port 80)** – For accessing the web app.

### Step 3: Install Docker on EC2

Connect to your EC2 instance via SSH and run the following commands:

```bash
# Update installed packages
sudo yum update -y

# Install Docker
sudo yum install -y docker

# Start Docker service
sudo service docker start

# Add ec2-user to the docker group (allows running docker without sudo)
sudo usermod -aG docker ec2-user
```

> **Important:** Log out and log back in for the group changes to take effect.

Verify the installation:

```bash
docker --version
```

### Step 4: Pull Docker Image

Pull the pre-built Node.js application image:

```bash
docker pull philippaul/node-dynamodb-demo
```

### Step 5: IAM Configuration

> **⚠️ Security Warning:** It is best practice to use **IAM Roles** attached to the EC2 instance rather than hardcoding AWS keys. However, for this demo, you can use environment variables.

If creating an **IAM User**:

1. Go to **IAM Dashboard** → **Users** → **Create User**.
2. Attach the `AmazonDynamoDBFullAccess` policy (use least privilege for production).
3. Generate **Access Key ID** and **Secret Access Key**.

### Step 6: Run Docker Container

Run the container with the necessary environment variables to connect to DynamoDB.

```bash
docker run --rm -d -p 80:3000 --name node-dynamo-app \
  -e AWS_REGION=us-east-1 \
  -e AWS_ACCESS_KEY_ID=YOUR_ACCESS_KEY \
  -e AWS_SECRET_ACCESS_KEY=YOUR_SECRET_KEY \
  philippaul/node-dynamodb-demo
```

**Command Breakdown:**

- `--rm`: Automatically remove the container when it stops.
- `-d`: Run in detached mode (background).
- `-p 80:3000`: Map EC2 port **80** to container port **3000**.
- `-e`: Set environment variables for AWS credentials and region.

### Step 7: Access the Application

Open your browser and navigate to your EC2 instance's **Public IP**:

```
http://<your-ec2-public-ip>
```

You should see the application live! 🎉

---

## 🔐 Security Best Practices

The method above uses Access Keys which is **NOT** recommended for production security.

### ✅ Verification: The Better Approach

Instead of passing keys:

1. Create an **IAM Role** with `AmazonDynamoDBFullAccess`.
2. Attach this IAM Role to your **EC2 Instance**.
3. Run the Docker container **without** passing `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY`. The AWS SDK will automatically use the instance metadata credentials.

---

## 🏗 Future Improvements

To take this project to an enterprise level, consider adding:

- **CloudWatch Logs:** For monitoring application logs.
- **Load Balancer (ALB) + SSL:** For HTTPS security.
- **Auto Scaling Group (ASG):** For high availability.
- **Docker Compose:** For managing multi-container setups.
- **CI/CD Pipeline:** For automated deployments.

---

## ⚠️ Troubleshooting

**1. App not loading?**

- Check if the container is running:
  ```bash
  docker ps
  ```
- Check security groups to ensure Port 80 is open.

**2. DynamoDB connection error?**

- Verify the **Region** matches your DynamoDB table.
- Check IAM permissions.
- Ensure the Table Name is exactly `Contacts`.

---

## 📊 Learning Outcomes

By completing this project, you have demonstrated skills in:

- ✅ **Cloud Deployment:** Running apps on EC2.
- ✅ **Containerization:** Using Docker for application hosting.
- ✅ **Database Integration:** connecting a stateless app to NoSQL DynamoDB.
- ✅ **IAM Security:** Managing permissions and access control.
- ✅ **DevOps Fundamentals:** Infrastructure setup and configuration.
