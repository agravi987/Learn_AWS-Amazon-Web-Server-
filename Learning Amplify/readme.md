# 🚀 Deploy a React App to AWS Amplify

> **A step-by-step guide to deploy your React application from GitHub to AWS Amplify with continuous deployment.**

---

## 📋 Table of Contents

- [Prerequisites](#-prerequisites)
- [Architecture Overview](#-architecture-overview)
- [Step 1 — Prepare Your GitHub Repository](#1️⃣-step-1--prepare-your-github-repository)
- [Step 2 — Connect GitHub Repo in AWS Amplify](#2️⃣-step-2--connect-github-repo-in-aws-amplify)
- [Step 3 — Select Repository & Branch](#3️⃣-step-3--select-repository--branch)
- [Step 4 — Build & Deployment](#4️⃣-step-4--build--deployment)
- [Key Takeaways](#-key-takeaways)
- [Cleanup](#🧹-cleanup)

---

## 🧠 Prerequisites

Before you begin, make sure you have the following ready:

| #   | Requirement        | Details                                                                                         |
| --- | ------------------ | ----------------------------------------------------------------------------------------------- |
| 1   | **GitHub Account** | With your React project pushed to a repository                                                  |
| 2   | **AWS Account**    | Free tier is sufficient                                                                         |
| 3   | **Branch**         | Your project's default branch should be `main` (or whichever branch you want Amplify to deploy) |

---

## 🏗️ Architecture Overview

```
┌─────────────┐       push        ┌────────────┐     auto-build     ┌──────────────────┐
│   Developer  │  ──────────────▶  │   GitHub    │  ──────────────▶  │   AWS Amplify     │
│  (Local Dev) │                   │ Repository  │                   │  (Hosting + CI/CD)│
└─────────────┘                    └────────────┘                    └────────┬─────────┘
                                                                             │
                                                                             ▼
                                                                    ┌──────────────────┐
                                                                    │   Live Website    │
                                                                    │  (amplifyapp.com) │
                                                                    └──────────────────┘
```

**How it works:**

1. You push code to GitHub
2. AWS Amplify detects the change automatically
3. Amplify builds and deploys your app
4. Your live site is updated — **zero manual intervention!**

---

## 1️⃣ Step 1 — Prepare Your GitHub Repository

Go to [GitHub](https://github.com) and create a **new repository** (or use an existing one).

Push your React app code into the repository:

```bash
git init
git remote add origin https://github.com/your-username/your-repo.git
git add .
git commit -m "initial commit"
git branch -M main
git push -u origin main
```

> [!TIP]
> This ensures your app is in GitHub and ready for Amplify to read from.

---

## 2️⃣ Step 2 — Connect GitHub Repo in AWS Amplify

1. **Log in** to the [AWS Management Console](https://console.aws.amazon.com)
2. Open the **AWS Amplify Console** (search for _"Amplify"_ in the search bar)
3. Click **"Deploy"** or **"Get Started"**
4. Choose **GitHub** as the repository provider
5. AWS will prompt you to **authorize access** to your GitHub account

> [!IMPORTANT]
> You may need to install the **AWS Amplify GitHub App** and grant it access to the specific repository you want to deploy. This is part of GitHub's recommended integration method.

---

## 3️⃣ Step 3 — Select Repository & Branch

After authorizing GitHub:

1. **Choose the repository** you created for your React app
2. **Select the branch** you want to deploy (e.g., `main`)
3. Click **Next** → **Save & Deploy** to start the deployment

### 🔄 What Amplify Does Next

| Action     | Description                                                                                      |
| ---------- | ------------------------------------------------------------------------------------------------ |
| **Build**  | Compiles your app using the default build settings from your project                             |
| **Deploy** | Hosts the built files and makes them accessible via a public URL                                 |
| **CI/CD**  | Sets up continuous deployment — every future `git push` triggers an automatic rebuild & redeploy |

---

## 4️⃣ Step 4 — Build & Deployment

⏱️ The build and deployment process typically takes **~3–5 minutes**.

Once complete:

- ✅ Amplify will show a **"Deployed"** status
- 🌐 A **live URL** will be generated — click it to see your app running!
- 🔁 Every future push to the connected branch on GitHub will **automatically trigger a new build and deployment**

---

## 🎯 Key Takeaways

| Concept                   | Summary                                                                       |
| ------------------------- | ----------------------------------------------------------------------------- |
| **AWS Amplify**           | A fully managed CI/CD and hosting service for modern web apps                 |
| **Continuous Deployment** | Push to GitHub → Amplify auto-builds → site is updated                        |
| **Zero Config**           | Amplify auto-detects React build settings (`npm run build`)                   |
| **Free Tier**             | AWS Free Tier covers a generous amount of build minutes and hosting bandwidth |

---

## 🧹 Cleanup

To avoid any unexpected charges, remember to **delete your Amplify app** when you're done learning:

1. Go to the **AWS Amplify Console**
2. Select your app
3. Click **Actions** → **Delete app**

---

> **Happy Deploying!** 🎉 You've successfully set up a CI/CD pipeline from GitHub to AWS Amplify.
