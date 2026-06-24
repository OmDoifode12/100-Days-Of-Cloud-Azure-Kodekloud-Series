# Day 36 – Managing Storage Lifecycle in Azure

---

## Task Overview

As part of a cloud cost optimization initiative, the Nautilus DevOps Team needed to automate data retention within Azure Blob Storage. The objective was to create a Storage Account, upload data to a Blob Container, and configure a Lifecycle Management Policy that automatically deletes blobs after 7 days of modification.

The following requirements were completed:

* Create a Storage Account named `datacenterstor15799`
* Configure the Storage Account in the `East US` region
* Use `Locally Redundant Storage (LRS)`
* Create a private Blob Container named `datacenter-container15799`
* Upload the file `tempfile.txt`
* Configure a Lifecycle Management Rule named `datacenter-del-rule`
* Automatically delete blobs after 7 days of last modification
* Verify that the lifecycle policy was successfully applied

---

# Architecture Overview

```text
Azure Storage Account
datacenterstor15799
        │
        ▼
Blob Container
datacenter-container15799
        │
        ▼
tempfile.txt
        │
        ▼
Lifecycle Management Policy
datacenter-del-rule
        │
        ▼
Delete Blob After 7 Days
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

# Step 2: Verify Resource Group

Run:

```bash
RG_NAME=$(az group list --query '[0].name' -o tsv)

echo $RG_NAME
```

#### Explanation

Retrieves the default resource group created for the lab environment.

---

# Step 3: Create Storage Account

Run:

```bash
az storage account create \
  --name datacenterstor15799 \
  --resource-group $RG_NAME \
  --location eastus \
  --sku Standard_LRS
```

#### Explanation

Creates a Storage Account using Locally Redundant Storage.

| Setting | Value |
|----------|---------|
| Name | datacenterstor15799 |
| Region | East US |
| Redundancy | LRS |

---

# Step 4: Create Blob Container

Run:

```bash
az storage container create \
  --name datacenter-container15799 \
  --account-name datacenterstor15799 \
  --auth-mode login
```

#### Explanation

Creates a private Azure Blob Container.

---

# Step 5: Upload File to Blob Storage

Run:

```bash
az storage blob upload \
  --account-name datacenterstor15799 \
  --container-name datacenter-container15799 \
  --name tempfile.txt \
  --file /root/tempfile.txt \
  --auth-mode login
```

#### Explanation

Uploads the required file into the container.

---

# Step 6: Create Lifecycle Management Policy File

Run:

```bash
cat > lifecycle.json <<EOF
{
  "rules": [
    {
      "enabled": true,
      "name": "datacenter-del-rule",
      "type": "Lifecycle",
      "definition": {
        "actions": {
          "baseBlob": {
            "delete": {
              "daysAfterModificationGreaterThan": 7
            }
          }
        },
        "filters": {
          "blobTypes": [
            "blockBlob"
          ],
          "prefixMatch": [
            "datacenter-container15799"
          ]
        }
      }
    }
  ]
}
EOF
```

#### Explanation

Creates the lifecycle policy definition that automatically deletes blobs after 7 days.

---

# Step 7: Apply Lifecycle Management Policy

Run:

```bash
az storage account management-policy create \
  --account-name datacenterstor15799 \
  --resource-group $RG_NAME \
  --policy @lifecycle.json
```

#### Explanation

Applies the lifecycle rule to the Storage Account.

---

# Step 8: Verify Lifecycle Management Policy

Run:

```bash
az storage account management-policy show \
  --account-name datacenterstor15799 \
  --resource-group $RG_NAME
```

#### Explanation

Verifies that the policy has been successfully applied.

Expected Output:

```json
"name": "datacenter-del-rule",
"type": "Lifecycle",
"enabled": true
```

---

# Final Validation Checklist

✅ Storage Account created successfully

✅ Blob Container created successfully

✅ File uploaded to Azure Blob Storage

✅ Lifecycle Management Rule configured

✅ Automatic deletion configured after 7 days

✅ Policy verification completed

✅ Task completed successfully

---

# Issues Faced During the Lab

| Issue | Resolution |
|---------|-----------|
| Policy not visible immediately | Re-ran policy show command |
| JSON formatting errors | Validated lifecycle.json structure |
| Blob upload authentication issue | Used `--auth-mode login` |
| Container not found | Verified storage account and container names |

---

# Best Practices

* Use Lifecycle Management to reduce storage costs
* Keep Blob Containers private whenever possible
* Automate retention policies instead of manual cleanup
* Verify policy configuration after deployment
* Regularly review storage usage and retention requirements

---

# Key Learnings

* Azure Lifecycle Management automates storage cleanup
* Blob retention policies help reduce operational overhead
* Storage Accounts support policy-driven data management
* Azure CLI simplifies storage automation tasks
* Automated deletion policies improve cost optimization strategies
