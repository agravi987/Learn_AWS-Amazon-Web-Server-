# ☁️ AWS CloudFront — Static Website Hosting Guide 💕

> _Delivering your content to the world, super fast and super secure!_ 🌍✨

---

## 📑 Table of Contents

1. [Create S3 Bucket](#-step-1-create-s3-bucket)
2. [Upload Files to Bucket](#-step-2-upload-files-to-bucket)
3. [Create CloudFront Distribution](#-step-3-create-cloudfront-distribution)
4. [Test Your Site](#-step-4-test-your-site)
5. [Quick Tips & Gotchas](#-quick-tips--gotchas)

---

## 🪣 Step 1: Create S3 Bucket

First, let's set up your S3 bucket to store your website files! 🗂️

1. Log into **AWS Console** → go to **S3** → click **"Create bucket"**.
2. Fill in the details:

| Setting                 | Value                                                          |
| :---------------------- | :------------------------------------------------------------- |
| **Bucket Name**         | Unique, lowercase (e.g., `ravi-static-site-2026`) — no spaces! |
| **Region**              | Pick one near you (e.g., `ap-south-1` for Mumbai 🇮🇳)           |
| **Block Public Access** | ✅ Keep it **checked** — CloudFront will handle access for us! |

3. Skip advanced settings → click **"Create bucket"**. 🎉

> [!NOTE]
> We keep the bucket **private** on purpose! CloudFront will securely fetch content from it using OAC (Origin Access Control). Much safer than public buckets! 🔒

---

## 📤 Step 2: Upload Files to Bucket

Time to fill your bucket with your awesome website files! 🌟

1. Open your newly created bucket.
2. Click **"Upload"** → drag & drop your files (e.g., `index.html`, `style.css`, images).
3. No special permissions needed — CloudFront will be configured to access them automatically.

> [!TIP]
> **Organize your files!** You can keep everything in the root (`/`) or use folders like `/images/`, `/css/` for a cleaner structure.

---

## 🚀 Step 3: Create CloudFront Distribution

This is where the magic happens! ✨ Let's connect CloudFront to your S3 bucket.

1. Go to **AWS Console** → **CloudFront** → click **"Create distribution"**.
2. Configure the settings:

| Setting                 | Value                                                  |
| :---------------------- | :----------------------------------------------------- |
| **Distribution Name**   | Something fun like `ravi-site-frontend` 😊             |
| **Website Type**        | Choose **"Single website or app"** → Next              |
| **Origin Type**         | **Amazon S3** → Browse S3 → Select your bucket         |
| **Origin Settings**     | Use recommended settings (auto-sets **OAC** — secure!) |
| **Default Root Object** | `index.html` (so the root URL loads your page!)        |
| **Security (WAF)**      | Optional for now → Next → **Create distribution**      |

3. ⏳ **Wait 5–15 minutes** for deployment (`Status: Deploying → Enabled`).
4. 📋 **Copy the Domain name** (e.g., `d123.cloudfront.net`). You'll need this!

> [!IMPORTANT]
> The distribution takes a few minutes to fully deploy. Grab a ☕ coffee and come back!

---

## 🧪 Step 4: Test Your Site

Let's see your work live! 🎊

1. Open your browser and visit:
   ```
   https://your-domain.cloudfront.net
   ```
   or try:
   ```
   https://your-domain.cloudfront.net/index.html
   ```
2. Your site should load beautifully and **globally fast** thanks to CloudFront Edge Locations! 🌏

> [!NOTE]
> Trying to access the S3 bucket URL directly? It will be **blocked** — and that's perfectly fine! That's OAC securing your content. 🛡️🎉

---

## 💡 Quick Tips & Gotchas

Helpful things to keep in mind as you learn! 🐾

| Tip                     | Details                                                                                   |
| :---------------------- | :---------------------------------------------------------------------------------------- |
| ⏰ **Propagation Time** | Changes take ~15 mins to reach all edge locations. Patience is key!                       |
| 🔐 **HTTPS**            | Auto-enabled by CloudFront. Enjoy the 🟢 green lock!                                      |
| 💸 **Costs**            | The Free Tier covers the basics. Watch your data transfer usage.                          |
| 🌐 **Custom Domain**    | Add a CNAME like `www.yoursite.dev` in **Distributions → Edit → Alternate domain names**. |
| 🔄 **Invalidate Cache** | If updates aren't showing up → **CloudFront → Invalidations → Create** → Path: `/*`       |

> [!TIP]
> **Invalidating the cache** is the most common fix when you update a file but the old version keeps showing. Always start there! 😊

---

_Happy learning! You're doing amazing! 💕🌟_
