# 📘 Configuring and Verifying Amazon S3 Cross-Region Replication

## 🎯 Objective

To configure **S3 Cross-Region Replication (CRR)** between two buckets and verify that objects uploaded to the source bucket are automatically replicated to the destination bucket.

## 🧠 Concept Overview

S3 Replication allows automatic copying of objects from one bucket (source) to another bucket (destination) in the same or different AWS regions.

### Types of Replication:

- **SRR (Same Region Replication)**: Source and destination buckets are in the same region.
- **CRR (Cross Region Replication)**: Source and destination buckets are in different regions.

> **Note:** We will be configuring **CRR** (Cross-Region Replication).

## 🏗️ Architecture Overview

```mermaid
graph LR
    A[Source Bucket (Region A)] -->|Replication Rule| B[Destination Bucket (Region B)]
```

## ⚠️ Important Prerequisites

Before replication works, ensure the following:

- [x] **Versioning** must be enabled on both source and destination buckets.
- [x] **Buckets** must be in different regions (for CRR).
- [x] **IAM Role** must allow replication permissions.
- [x] Only objects uploaded **AFTER** the rule is created will be replicated.

---

## 🚀 Step 1: Create Source Bucket

1. Go to the **S3 Console**.
2. Click **Create Bucket**.
3. **Bucket Name**: `ravi-source-bucket-unique` (Enable unique naming).
4. **Region**: Select a region (e.g., `Mumbai`).
5. Keep default settings for the rest.
6. Click **Create bucket**.

## 🚀 Step 2: Create Destination Bucket

1. Click **Create Bucket**.
2. **Bucket Name**: `ravi-destination-bucket-unique`.
3. **Region**: Select a **different** region (e.g., `Singapore`).
4. Keep default settings.
5. Click **Create bucket**.

## 🔄 Step 3: Enable Versioning (MANDATORY)

> **Crucial Step:** Without versioning, replication will **not** work.

**For BOTH buckets (Source & Destination):**

1. Go to the **Bucket** → **Properties** tab.
2. Scroll to **Bucket Versioning**.
3. Click **Edit** → Select **Enable**.
4. Click **Save changes**.

## 🔐 Step 4: Configure Replication Rule

1. Navigate to your **Source Bucket**.
2. Go to the **Management** tab.
3. Scroll to **Replication rules** and click **Create replication rule**.

### Rule Configuration:

- **Replication rule name**: `CRR-Rule`
- **Status**: `Enabled`

### Source Bucket:

- **Choose a rule scope**: `Apply to all objects in the bucket`

### Destination:

- **Destination**: Choose a bucket in this account.
- **Bucket name**: Select your **Destination Bucket** (`ravi-destination-bucket-unique`).

### IAM Role:

- **IAM Role**: Select **Create new role**.
  - _AWS will automatically create the required IAM role with correct permissions._

Click **Save**. When prompted to replicate existing objects, you can select "No, do not replicate existing objects" if your bucket is empty.

---

## 📤 Step 5: Test Replication

Now, let's verify if the replication is working.

1. Upload a new file (e.g., `test-file.txt`) to the **Source Bucket**.
2. Wait for **10–30 seconds**.
3. Navigate to the **Destination Bucket**.
4. You should see the **same file** with the **same name**.

## 🔎 Step 6: Verify Replication Status

1. Go back to the **Source Bucket**.
2. Click on the file you just uploaded.
3. Under the **Properties** tab, scroll down to **Replication status**.
4. You should see:
   - **Replication status**: `COMPLETED`

> **If Status is FAILED or PENDING for a long time:**
>
> - Check IAM Role permissions.
> - Ensure Versioning is enabled on BOTH buckets.
> - Verify Region difference.

## 🧪 Step 7: Versioning Verification Test

1. Modify the `test-file.txt` locally (add some text).
2. Upload the modified file again to the **Source Bucket**.
3. Go to the **Destination Bucket**.
4. Toggle **Show versions**.
5. You should see multiple versions of the file.

✅ **Result:** This confirms that Cross-Region Replication with Versioning is working correctly!
