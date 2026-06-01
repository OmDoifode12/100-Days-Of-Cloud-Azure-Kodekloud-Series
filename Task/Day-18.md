# Day 18 – Copy Data to an Azure Blob Storage Container

---

## Task Overview

The Nautilus DevOps team is presently immersed in data migrations, transferring data from on-premise storage systems to Azure Blob containers. They have recently received some data that they intend to copy to one of the Blob containers.

For this task:

| Resource        | Name                |
| --------------- | ------------------- |
| Storage Account | `devopsst4689`      |
| Blob Container  | `devops-blob-31149` |
| Source File     | `/tmp/devops.txt`   |

Your objective is to:

* Upload the file `/tmp/devops.txt`
* Copy it to the Blob Container `devops-blob-31149`
* Verify the file upload successfully

---

# Step-by-Step Implementation (Azure Portal UI)

### Step 1: Login to Azure Portal

Open the Azure Portal:

https://portal.azure.com

Login using your Azure account credentials.

#### Explanation

The Azure Portal is Microsoft's web-based management interface used to manage Azure resources.

---

### Step 2: Navigate to Storage Accounts

Use the search bar and search for:

| Search           |
| ---------------- |
| Storage Accounts |

Open the Storage Account:

| Storage Account |
| --------------- |
| `devopsst4689`  |

#### Explanation

The Storage Account contains the Blob Container where the file will be uploaded.

---

### Step 3: Open Blob Container

Navigate to:

| Section                   |
| ------------------------- |
| Data Storage → Containers |

Open:

| Container           |
| ------------------- |
| `devops-blob-31149` |

#### Explanation

Blob Containers store files and objects inside Azure Storage.

---

### Step 4: Upload File

Click:

* **Upload**

Select the file:

```bash
/tmp/devops.txt
```

Click:

* **Upload**

#### Explanation

Azure uploads the file as a Blob object into the container.

---

### Step 5: Verify Upload

Verify:

| Blob Name  |
| ---------- |
| devops.txt |

#### Explanation

This confirms that the file has been successfully uploaded to Azure Blob Storage.

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

### Step 2: Retrieve Storage Account Key

Run:

```bash
ACCOUNT_KEY=$(az storage account keys list \
  --account-name devopsst4689 \
  --query "[0].value" \
  -o tsv)
```

#### Explanation

Retrieves the Storage Account access key required for Blob operations.

---

### Step 3: Upload File to Blob Container

Run:

```bash
az storage blob upload \
  --account-name devopsst4689 \
  --container-name devops-blob-31149 \
  --name devops.txt \
  --file /tmp/devops.txt \
  --account-key $ACCOUNT_KEY
```

#### Explanation

This command uploads the file `/tmp/devops.txt` to the Blob Container `devops-blob-31149`.

---

### Step 4: Verify Blob Upload

Run:

```bash
az storage blob list \
  --account-name devopsst4689 \
  --container-name devops-blob-31149 \
  --account-key $ACCOUNT_KEY \
  --output table
```

Expected Output:

| Name       |
| ---------- |
| devops.txt |

#### Explanation

This command lists all blobs inside the container and confirms successful upload.

---

# Best Practices

* Use Azure Blob Storage for scalable object storage
* Apply RBAC permissions whenever possible
* Encrypt sensitive files before uploading
* Organize data using containers and folders
* Monitor storage usage and access patterns

---

# Key Learnings

* Azure Blob Storage is used for storing files and objects
* Blob Containers organize data within Storage Accounts
* Azure CLI simplifies file upload operations
* Storage Account keys provide authentication for storage actions
* Blob uploads are a common task during cloud migration projects
