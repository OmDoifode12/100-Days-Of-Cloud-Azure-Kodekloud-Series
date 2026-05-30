# Day 16 – Create a Private Azure Blob Storage Container

---

## Task Overview

As part of the data migration process, the Nautilus DevOps team is actively creating several storage containers on Azure. They plan to utilize private Blob containers to store the relevant data. Given the ongoing migration of other infrastructure to Azure, it is logical to consolidate data storage within the Azure environment as well.

For this task, create the following Azure Storage resources:

| Resource        | Name                    |
| --------------- | ----------------------- |
| Storage Account | `datacenterst9990`      |
| Blob Container  | `datacenter-blob-13742` |

Additional Requirement:

* The Blob Container must be **Private** (No anonymous access).

---

# Step-by-Step Implementation (Azure Portal UI)

### Step 1: Login to Azure Portal

Open the Azure Portal:

https://portal.azure.com

Login using your Azure account credentials.

#### Explanation

The Azure Portal is Microsoft's web-based management interface used to create and manage Azure resources.

---

### Step 2: Navigate to Storage Accounts

Use the search bar and search for:

| Search           |
| ---------------- |
| Storage Accounts |

Open the **Storage Accounts** service.

#### Explanation

A Storage Account acts as the parent resource for Azure Blob Storage, File Shares, Queues, and Tables.

---

### Step 3: Create Storage Account

Click:

* **+ Create**

Configure:

| Setting              | Value                           |
| -------------------- | ------------------------------- |
| Subscription         | Default Azure Subscription      |
| Resource Group       | Existing Resource Group         |
| Storage Account Name | `datacenterst9990`              |
| Region               | Same region as lab resources    |
| Performance          | Standard                        |
| Redundancy           | Locally Redundant Storage (LRS) |

Click:

* **Review + Create**
* **Create**

#### Explanation

This creates the Storage Account that will host the Blob Container.

---

### Step 4: Open Storage Account

After deployment completes:

Open:

| Storage Account  |
| ---------------- |
| datacenterst9990 |

#### Explanation

The Storage Account must exist before creating containers.

---

### Step 5: Navigate to Containers

Inside the Storage Account:

Navigate to:

| Section                   |
| ------------------------- |
| Data Storage → Containers |

Click:

* **+ Container**

#### Explanation

Blob Containers are logical containers used to store blobs (objects/files).

---

### Step 6: Create Private Blob Container

Configure:

| Setting             | Value                         |
| ------------------- | ----------------------------- |
| Name                | `datacenter-blob-13742`       |
| Public Access Level | Private (No anonymous access) |

Click:

* **Create**

#### Explanation

A private container restricts access to authenticated users only.

---

### Step 7: Verify Container Creation

Verify:

| Property       | Expected Value        |
| -------------- | --------------------- |
| Container Name | datacenter-blob-13742 |
| Access Level   | Private               |

#### Explanation

This confirms successful creation of the Blob Container.

---

# Method 2: Using Azure CLI

### Step 1: Login to Azure

Run:

```bash
az login
```

#### Explanation

This command authenticates Azure CLI with your Azure account.

---

### Step 2: Check Available Resource Groups

Run:

```bash
az group list --output table
```

#### Explanation

This command lists all available Resource Groups.

---

### Step 3: Set Resource Group Variable

Run:

```bash
RG_NAME="kml_rg_main-145620288f634136"
```

#### Explanation

Stores the Resource Group name for easier command execution.

---

### Step 4: Create Storage Account

Run:

```bash
az storage account create \
  --resource-group $RG_NAME \
  --name datacenterst9990 \
  --sku Standard_LRS
```

#### Explanation

Creates a Storage Account using Standard Locally Redundant Storage.

---

### Step 5: Retrieve Storage Account Key

Run:

```bash
ACCOUNT_KEY=$(az storage account keys list \
  --resource-group $RG_NAME \
  --account-name datacenterst9990 \
  --query "[0].value" \
  -o tsv)
```

#### Explanation

Retrieves the access key required to manage storage resources.

---

### Step 6: Create Private Blob Container

Run:

```bash
az storage container create \
  --account-name datacenterst9990 \
  --name datacenter-blob-13742 \
  --public-access off \
  --account-key $ACCOUNT_KEY
```

#### Explanation

Creates a Blob Container with private access permissions.

---

### Step 7: Verify Container Creation

Run:

```bash
az storage container list \
  --account-name datacenterst9990 \
  --account-key $ACCOUNT_KEY \
  --output table
```

Expected Output:

| Name                  |
| --------------------- |
| datacenter-blob-13742 |

#### Explanation

This confirms that the Blob Container was successfully created.

---

# Best Practices

* Use private containers for sensitive data
* Follow proper naming conventions
* Enable encryption for storage accounts
* Implement role-based access control (RBAC)
* Monitor storage usage and access logs regularly

---

# Key Learnings

* Azure Blob Storage provides scalable object storage
* Storage Accounts are the foundation of Azure storage services
* Blob Containers organize and store objects within a Storage Account
* Private containers restrict anonymous access
* Azure CLI enables efficient storage management and automation
