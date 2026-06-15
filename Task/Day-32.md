# Day 32 – Synchronizing Containers Using Azure CLI

---

## Task Overview

As part of a cloud data migration project, the Nautilus DevOps Team was tasked with migrating data from an existing Azure Blob container to a newly created private Blob container. The objective was to securely copy the required blob file while ensuring complete data consistency between the source and destination containers.

The following requirements were completed:

* Create a private Azure Blob container named `datacenter-dest-29512`
* Use the existing storage account `datacenterst16927`
* Copy the file `datacenter.txt` from source container `datacenter-source-6733`
* Verify the file exists in both containers
* Ensure the file content is identical after migration
* Perform the entire task using Azure CLI

---

# Architecture Overview

```text id="m7q2x5"
Source Blob Container
datacenter-source-6733
        │
        ▼
datacenter.txt
        │
        ▼
Azure CLI Blob Copy
        │
        ▼
Destination Blob Container
datacenter-dest-29512
```

---

# Step-by-Step Implementation Using Azure CLI

---

# Step 1: Login to Azure

Run:

```bash id="p4x8m1"
az login
```

#### Explanation

Authenticates Azure CLI with the Azure subscription.

---

# Step 2: Verify Azure Subscription

Run:

```bash id="v1m6q9"
az account show
```

#### Explanation

Verifies the currently active Azure subscription.

---

# Step 3: Create Destination Blob Container

Run:

```bash id="k8m3x2"
az storage container create \
  --name datacenter-dest-29512 \
  --account-name datacenterst16927 \
  --auth-mode login
```

#### Explanation

Creates a new private Azure Blob container.

| Setting        | Value                   |
| -------------- | ----------------------- |
| Container Name | `datacenter-dest-29512` |
| Access Type    | Private                 |

---

# Step 4: Verify Source Blob File

Run:

```bash id="q5v1m7"
az storage blob list \
  --account-name datacenterst16927 \
  --container-name datacenter-source-6733 \
  --auth-mode login \
  --output table
```

Expected Output:

| Blob Name        |
| ---------------- |
| `datacenter.txt` |

#### Explanation

Confirms the source blob file exists before migration.

---

# Step 5: Copy Blob to Destination Container

Run:

```bash id="t4m9x2"
az storage blob copy start \
  --account-name datacenterst16927 \
  --destination-container datacenter-dest-29512 \
  --destination-blob datacenter.txt \
  --source-account-name datacenterst16927 \
  --source-container datacenter-source-6733 \
  --source-blob datacenter.txt \
  --auth-mode login
```

#### Explanation

Copies the blob file from source container to destination container within Azure Storage.

---

# Step 6: Verify Blob in Destination Container

Run:

```bash id="n6q1v8"
az storage blob list \
  --account-name datacenterst16927 \
  --container-name datacenter-dest-29512 \
  --auth-mode login \
  --output table
```

Expected Output:

| Blob Name        |
| ---------------- |
| `datacenter.txt` |

#### Explanation

Confirms the blob file exists in the destination container.

---

# Step 7: Download Source Blob

Run:

```bash id="k3x7m5"
az storage blob download \
  --account-name datacenterst16927 \
  --container-name datacenter-source-6733 \
  --name datacenter.txt \
  --file /tmp/source.txt \
  --auth-mode login
```

#### Explanation

Downloads the source blob locally for verification.

---

# Step 8: Download Destination Blob

Run:

```bash id="r2p8q4"
az storage blob download \
  --account-name datacenterst16927 \
  --container-name datacenter-dest-29512 \
  --name datacenter.txt \
  --file /tmp/destination.txt \
  --auth-mode login
```

#### Explanation

Downloads the destination blob locally for comparison.

---

# Step 9: Verify File Consistency

Run:

```bash id="v5m1x9"
diff /tmp/source.txt /tmp/destination.txt
```

Expected Output:

```text id="q7p3m2"
(no output)
```

#### Explanation

If no output appears, both files are identical and the migration was successful.

---

# Final Validation Checklist

✅ Private destination Blob container created
✅ Blob copied successfully from source container
✅ Blob exists in both source and destination containers
✅ File integrity verified successfully
✅ No data corruption or loss detected
✅ Entire task completed using Azure CLI

---

# Common Issues & Fixes

| Issue                    | Resolution                    |
| ------------------------ | ----------------------------- |
| Authorization failed     | Re-run `az login`             |
| Blob not found           | Verify blob/container names   |
| Copy operation failed    | Retry blob copy command       |
| Permission denied        | Use `--auth-mode login`       |
| File mismatch after copy | Re-download and compare again |

---

# Best Practices

* Always verify file integrity after migrations
* Use private Blob containers for secure storage
* Automate Blob synchronization for large migrations
* Validate copied data using checksum or diff tools
* Restrict unnecessary public storage access

---

# Key Learnings

* Azure Blob Storage provides scalable cloud object storage
* Azure CLI simplifies storage and migration operations
* Blob copy operations can occur entirely within Azure
* File consistency verification is essential during migrations
* Private Blob containers improve storage security
