# Day 17 – Create a Public Azure Blob Storage Container

---

## Task Overview

As part of the data migration process, the Nautilus DevOps team is actively creating several storage containers on Azure. They plan to utilize public Blob containers to store the relevant data. Given the ongoing migration of other infrastructure to Azure, it is logical to consolidate data storage within the Azure environment as well.

For this task, create the following Azure Storage resources:

| Resource        | Name                 |
| --------------- | -------------------- |
| Storage Account | `xfusionst22356`     |
| Blob Container  | `xfusion-blob-11514` |

Additional Requirement:

* Enable anonymous read access for containers and blobs.

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

Storage Accounts provide Azure Blob Storage and other storage services.

---

### Step 3: Create Storage Account

Click:

* **+ Create**

Configure:

| Setting              | Value                      |
| -------------------- | -------------------------- |
| Subscription         | Default Azure Subscription |
| Resource Group       | Existing Resource Group    |
| Storage Account Name | `xfusionst22356`           |
| Performance          | Standard                   |
| Redundancy           | LRS                        |

Click:

* **Review + Create**
* **Create**

#### Explanation

This creates the Storage Account that will host the Blob Container.

---

### Step 4: Enable Anonymous Blob Access

Open:

| Storage Account |
| --------------- |
| xfusionst22356  |

Navigate to:

| Section                  |
| ------------------------ |
| Settings → Configuration |

Verify:

| Setting                     | Value   |
| --------------------------- | ------- |
| Allow Blob Anonymous Access | Enabled |

Save changes if necessary.

#### Explanation

Anonymous access must be enabled before creating public containers.

---

### Step 5: Create Blob Container

Navigate to:

| Section                   |
| ------------------------- |
| Data Storage → Containers |

Click:

* **+ Container**

Configure:

| Setting             | Value                |
| ------------------- | -------------------- |
| Name                | `xfusion-blob-11514` |
| Public Access Level | Container            |

Click:

* **Create**

#### Explanation

Container-level access allows anonymous read access to both the container and blobs.

---

### Step 6: Verify Container

Verify:

| Property      | Expected Value     |
| ------------- | ------------------ |
| Name          | xfusion-blob-11514 |
| Public Access | Container          |

#### Explanation

This confirms successful creation of the public Blob Container.

---

# Method 2: Using Azure CLI

### Step 1: Login

```bash
az login
```

---

### Step 2: Set Resource Group

```bash
RG_NAME="kml_rg_main-145620288f634136"
```

---

### Step 3: Create Storage Account

```bash
az storage account create \
  --resource-group $RG_NAME \
  --name xfusionst22356 \
  --sku Standard_LRS \
  --allow-blob-public-access true
```

#### Explanation

Creates a Storage Account with public blob access enabled.

---

### Step 4: Retrieve Storage Account Key

```bash
ACCOUNT_KEY=$(az storage account keys list \
  --resource-group $RG_NAME \
  --account-name xfusionst22356 \
  --query "[0].value" \
  -o tsv)
```

---

### Step 5: Create Public Blob Container

```bash
az storage container create \
  --account-name xfusionst22356 \
  --name xfusion-blob-11514 \
  --public-access container \
  --account-key $ACCOUNT_KEY
```

#### Explanation

Creates a Blob Container with anonymous access enabled.

---

### Step 6: Verify Container

```bash
az storage container show \
  --account-name xfusionst22356 \
  --name xfusion-blob-11514 \
  --account-key $ACCOUNT_KEY
```

Expected Output:

```json
"publicAccess": "Container"
```

---

# Best Practices

* Enable public access only when necessary
* Store sensitive data in private containers
* Monitor storage access regularly
* Apply RBAC where possible
* Use naming conventions consistently

---

# Key Learnings

* Azure Blob Storage provides scalable object storage
* Public containers allow anonymous read access
* Storage Accounts manage Blob Containers
* Access levels control data visibility
* Azure CLI simplifies storage management
