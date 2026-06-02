# Day 19 – Convert Public Azure Blob Container to Private

---

## Task Overview

The Nautilus DevOps team has been using Azure Blob Storage to manage their data. Recently, they realized that one of their containers, currently public, needs to be restricted for internal use only.

For this task:

| Resource          | Name                       |
| ----------------- | -------------------------- |
| Storage Account   | `nautilusst4157`           |
| Public Container  | `nautilus-container-22620` |
| Private Container | `nautilus-priv-31721`      |

Your objective is to:

* Convert the Blob Container `nautilus-container-22620` from Public to Private
* Leave `nautilus-priv-31721` unchanged
* Ensure anonymous access is disabled for `nautilus-container-22620`

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

Open the Storage Account:

| Storage Account  |
| ---------------- |
| `nautilusst4157` |

#### Explanation

This Storage Account contains both Blob Containers used in this task.

---

### Step 3: Open Blob Containers

Navigate to:

| Section                   |
| ------------------------- |
| Data Storage → Containers |

#### Explanation

This displays all Blob Containers inside the Storage Account.

---

### Step 4: Open Public Container

Select:

| Container                  |
| -------------------------- |
| `nautilus-container-22620` |

#### Explanation

This is the container that currently has public access enabled.

---

### Step 5: Change Access Level

Click:

* **Change Access Level**

Configure:

| Setting             | Value                         |
| ------------------- | ----------------------------- |
| Public Access Level | Private (No Anonymous Access) |

Click:

* **Save**
* **OK**

#### Explanation

This removes all anonymous access and converts the container into a private container.

---

### Step 6: Verify Container Access

Verify:

| Property            | Expected Value           |
| ------------------- | ------------------------ |
| Container Name      | nautilus-container-22620 |
| Public Access Level | Private                  |

#### Explanation

This confirms that public access has been removed successfully.

---

### Step 7: Verify Existing Private Container

Open:

| Container             |
| --------------------- |
| `nautilus-priv-31721` |

Verify:

| Property            | Expected Value |
| ------------------- | -------------- |
| Public Access Level | Private        |

#### Explanation

This container should remain unchanged throughout the task.

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
  --account-name nautilusst4157 \
  --query "[0].value" \
  -o tsv)
```

#### Explanation

Retrieves the Storage Account access key required for container management.

---

### Step 3: Convert Container to Private

Run:

```bash
az storage container set-permission \
  --account-name nautilusst4157 \
  --name nautilus-container-22620 \
  --public-access off \
  --account-key $ACCOUNT_KEY
```

#### Explanation

This command removes anonymous access and converts the container to private.

---

### Step 4: Verify Container Permissions

Run:

```bash
az storage container show \
  --account-name nautilusst4157 \
  --name nautilus-container-22620 \
  --account-key $ACCOUNT_KEY
```

Expected Output:

```json
"publicAccess": null
```

#### Explanation

A null value indicates that no public access is configured and the container is private.

---

### Step 5: Verify Existing Private Container

Run:

```bash
az storage container show \
  --account-name nautilusst4157 \
  --name nautilus-priv-31721 \
  --account-key $ACCOUNT_KEY
```

#### Explanation

Confirms that the existing private container remains unchanged.

---

# Best Practices

* Keep sensitive data in private containers
* Disable anonymous access whenever possible
* Apply least-privilege access principles
* Regularly audit storage permissions
* Use Azure RBAC for fine-grained access control

---

# Key Learnings

* Azure Blob Containers support Public and Private access levels
* Private containers prevent anonymous access to data
* Storage security is a critical component of cloud architecture
* Azure CLI simplifies container permission management
* Proper access control reduces the risk of data exposure
