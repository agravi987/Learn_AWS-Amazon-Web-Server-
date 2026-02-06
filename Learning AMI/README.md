# EC2 Static Website Deployment using AMI

## 🚀 Project Overview

A beginner-friendly project to understand **Amazon Machine Images (AMIs)**. You will configure an EC2 instance with a website, save it as an AMI, and launch a clone to verify that your data persists.

---

## ✅ Prerequisites

- **AWS Account** (Free Tier)
- **PuTTY & PuTTYgen** (For SSH Connection)
- **WinSCP** (For file transfer)
- **Basic HTML File** (`index.html`) ready to upload

---

## 🛠️ Step-by-Step Guide

### **Step 1: Launch EC2 Instance**

1.  **Launch Instance:** Amazon Linux 2, t2.micro.
2.  **Security Group:** Allow **SSH (22)** and **HTTP (80)**.
3.  **Key Pair:** Download `.pem` file.
4.  **Convert Key:** Open **PuTTYgen**, load `.pem`, and save as `.ppk`.

### **Step 2: Connect via PuTTY**

1.  Open **PuTTY**.
2.  **Host Name:** `ec2-user@<Your-Public-IP>`
3.  **Auth Key:** Load your `.ppk` file under _SSH → Auth → Credentials_.
4.  Click **Open**.

### **Step 3: Install Web Server**

Run these commands in the PuTTY terminal:

```bash
# Update and install Apache
sudo yum update -y
sudo yum install httpd -y

# Start and enable the server
sudo systemctl start httpd
sudo systemctl enable httpd
```

### **Step 4: Upload Website (WinSCP)**

⚠️ **Crucial Step:** Before uploading, give permission to the upload folder. Run this in PuTTY:

```bash
sudo chown -R ec2-user:ec2-user /var/www/html
sudo chmod -R 755 /var/www/html
```

**Now Upload:**

1.  Open **WinSCP** and connect using your `.ppk` key.
2.  Go to the folder: `/var/www/html/`
3.  Drag and drop your `index.html` file here.
4.  Test by opening `http://<Your-Public-IP>` in a browser.

### **Step 5: Create AMI**

1.  Select your instance in AWS Console.
2.  Go to **Actions → Image and templates → Create image**.
3.  Name it `My-Web-Server-Image`.
4.  Wait for the status to change from _Pending_ to _Available_ in the **AMIs** section.

### **Step 6: Launch Clone & Verify**

1.  Go to **AMIs**, select your new image, and click **Launch instance from AMI**.
2.  Launch it (same settings: t2.micro, HTTP/SSH enabled).
3.  Copy the **new Public IP** and open it in your browser.
4.  **Result:** Your website should load instantly without re-installing anything! 🎉

---

## 🧹 Cleanup

To avoid charges, terminate both instances and delete the AMI/Snapshot when done.

---

## 👨‍💻 Author

**Your Name**

- GitHub: [@agravi987](https://github.com/agravi987)
