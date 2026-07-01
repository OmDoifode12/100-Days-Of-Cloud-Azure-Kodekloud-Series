# Day 41 – Working with Azure Table Storage

---

## Task Overview

As part of developing a simple cloud-based To-Do application, the Nautilus DevOps Team utilized Azure Table Storage to store and manage task information. The objective was to create an Azure Storage Account, provision a Table Storage table, insert task entities using Azure CLI, and verify the stored data.

The following requirements were completed:

* Create a Storage Account named `nautilustablest18469`
* Configure the Storage Account in the `East US` region
* Create a Table Storage table named `tasks`
* Insert two task entities using Azure CLI
* Verify Task 1 status is `completed`
* Verify Task 2 status is `in-progress`

---

# Architecture Overview

```text
Azure Storage Account
nautilustablest18469
        │
        ▼
Azure Table Storage
tasks
        │
        ├──────────────┐
        ▼              ▼
Task 1             Task 2
PartitionKey       PartitionKey
RowKey             RowKey
Description        Description
Status             Status
```

---

# Step-by-Step Implementation Using Azure CLI

---

# Step 1: Login to Azure

Run:

```bash
az login
```

#### Explanation

Authenticates Azure CLI with the Azure subscription.

---

# Step 2: Retrieve the Resource Group

Run:

```bash
RG_NAME=$(az group list --query "[0].name" -o tsv)

echo $RG_NAME
```

#### Explanation

Retrieves the default Resource Group created for the lab.

---

# Step 3: Create Storage Account

Run:

```bash
az storage account create \
  --name nautilustablest18469 \
  --resource-group $RG_NAME \
  --location eastus \
  --sku Standard_LRS
```

#### Explanation

Creates an Azure Storage Account using Locally Redundant Storage (LRS).

| Setting | Value |
|----------|-------|
| Storage Account | nautilustablest18469 |
| Region | East US |
| Redundancy | Standard LRS |

---

# Step 4: Retrieve Storage Account Access Key

Run:

```bash
ACCOUNT_KEY=$(az storage account keys list \
  --resource-group $RG_NAME \
  --account-name nautilustablest18469 \
  --query "[0].value" \
  -o tsv)

echo $ACCOUNT_KEY
```

#### Explanation

Retrieves the Storage Account access key required for Table Storage operations.

---

# Step 5: Create the Table

Run:

```bash
az storage table create \
  --name tasks \
  --account-name nautilustablest18469 \
  --account-key "$ACCOUNT_KEY"
```

#### Explanation

Creates the Azure Table Storage table named `tasks`.

---

# Step 6: Verify the Table

Run:

```bash
az storage table list \
  --account-name nautilustablest18469 \
  --account-key "$ACCOUNT_KEY" \
  -o table
```

Expected Output:

```text
tasks
```

#### Explanation

Verifies that the table has been created successfully.

---

# Step 7: Insert Task 1

Run:

```bash
az storage entity insert \
  --table-name tasks \
  --account-name nautilustablest18469 \
  --account-key "$ACCOUNT_KEY" \
  --entity PartitionKey=tasks RowKey=1 description="Learn Table Storage" status=completed
```

#### Explanation

Inserts the first task entity into the table.

---

# Step 8: Insert Task 2

Run:

```bash
az storage entity insert \
  --table-name tasks \
  --account-name nautilustablest18469 \
  --account-key "$ACCOUNT_KEY" \
  --entity PartitionKey=tasks RowKey=2 description="Build To-Do App" status="in-progress"
```

#### Explanation

Inserts the second task entity into the table.

---

# Step 9: Verify Task 1

Run:

```bash
az storage entity show \
  --table-name tasks \
  --account-name nautilustablest18469 \
  --account-key "$ACCOUNT_KEY" \
  --partition-key tasks \
  --row-key 1
```

Expected Output:

```json
"description": "Learn Table Storage",
"status": "completed"
```

#### Explanation

Confirms that Task 1 was inserted correctly.

---

# Step 10: Verify Task 2

Run:

```bash
az storage entity show \
  --table-name tasks \
  --account-name nautilustablest18469 \
  --account-key "$ACCOUNT_KEY" \
  --partition-key tasks \
  --row-key 2
```

Expected Output:

```json
"description": "Build To-Do App",
"status": "in-progress"
```

#### Explanation

Confirms that Task 2 was inserted correctly.

---

# Final Validation Checklist

✅ Azure Storage Account created

✅ Table Storage table created

✅ Storage Account access key retrieved

✅ Task 1 inserted successfully

✅ Task 2 inserted successfully

✅ Task 1 status verified as `completed`

✅ Task 2 status verified as `in-progress`

✅ Task completed successfully

---

# Issues Faced During the Lab

| Issue | Resolution |
|---------|-----------|
| `argument --account-key: expected one argument` | Retrieved the Storage Account access key before running storage commands |
| Storage commands failed initially | Stored the access key in the `ACCOUNT_KEY` variable |
| Authentication issue | Used the correct Storage Account access key |
| Verification required | Queried each entity individually to confirm data insertion |

---

# Best Practices

* Keep Storage Account keys secure.
* Use meaningful `PartitionKey` values for better scalability.
* Use unique `RowKey` values for entity identification.
* Verify inserted entities after every write operation.
* Prefer Azure CLI automation for repeatable cloud deployments.

---

# Key Learnings

* Creating Azure Storage Accounts
* Working with Azure Table Storage
* Creating and managing tables using Azure CLI
* Understanding `PartitionKey` and `RowKey`
* Inserting and querying entities
* Authenticating with Storage Account keys
* Managing NoSQL data in Azure
* Building lightweight cloud data storage solutions
