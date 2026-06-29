# Day 38 – Running Containers on Azure Virtual Machines

---

## Task Overview

As part of a cloud storage integration project, the Nautilus DevOps Team needed to configure an Azure Virtual Machine to securely interact with Azure Blob Storage. The objective was to create a private Storage Account, configure a Blob Container, generate an access key, and upload a file from the VM to Azure Blob Storage using Azure CLI.

The following requirements were completed:

* Verify the existing Azure VM `nautilus-vm`
* Create a Storage Account named `nautilusstor19767`
* Configure the Storage Account in the `East US` region
* Use `Locally Redundant Storage (LRS)`
* Create a private Blob Container named `nautilus-container19767`
* Retrieve the Storage Account access key
* Create a test file on the Azure VM
* Upload the file to Azure Blob Storage using Azure CLI
* Verify the uploaded Blob

---

# Architecture Overview

```text
Azure Virtual Machine
nautilus-vm
        │
        ▼
testfile.txt
        │
        ▼
Azure CLI
        │
        ▼
Azure Storage Account
nautilusstor19767
        │
        ▼
Private Blob Container
nautilus-container19767
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

Retrieves the default Resource Group created for the lab.

---

# Step 3: Verify Existing Virtual Machine

Run:

```bash
az vm list -d -o table
```

#### Explanation

Ensures the existing VM `nautilus-vm` is available before continuing.

---

# Step 4: Create Storage Account

Run:

```bash
az storage account create \
  --name nautilusstor19767 \
  --resource-group $RG_NAME \
  --location eastus \
  --sku Standard_LRS
```

#### Explanation

Creates a Storage Account using Locally Redundant Storage.

| Setting | Value |
|----------|-------|
| Storage Account | nautilusstor19767 |
| Region | East US |
| Redundancy | Standard LRS |

---

# Step 5: Create Private Blob Container

Run:

```bash
az storage container create \
  --account-name nautilusstor19767 \
  --name nautilus-container19767 \
  --auth-mode login
```

#### Explanation

Creates a private Blob Storage container.

---

# Step 6: Retrieve Storage Account Access Key

Run:

```bash
ACCOUNT_KEY=$(az storage account keys list \
  --resource-group $RG_NAME \
  --account-name nautilusstor19767 \
  --query "[0].value" \
  -o tsv)

echo $ACCOUNT_KEY
```

#### Explanation

Retrieves the Storage Account key required for Blob authentication.

---

# Step 7: Get VM Public IP

Run:

```bash
VM_IP=$(az vm show \
  --resource-group $RG_NAME \
  --name nautilus-vm \
  -d \
  --query publicIps \
  -o tsv)

echo $VM_IP
```

#### Explanation

Obtains the public IP of the Azure VM.

---

# Step 8: SSH into Azure VM

Run:

```bash
ssh azureuser@$VM_IP
```

#### Explanation

Connects to the Azure Virtual Machine.

---

# Step 9: Create Test File

Run:

```bash
echo "this is a test file" > /home/azureuser/testfile.txt
```

Verify:

```bash
cat /home/azureuser/testfile.txt
```

Expected Output:

```text
this is a test file
```

#### Explanation

Creates the file that will be uploaded to Azure Blob Storage.

---

# Step 10: Upload File to Azure Blob Storage

Run:

```bash
az storage blob upload \
  --account-name nautilusstor19767 \
  --account-key <ACCOUNT_KEY> \
  --container-name nautilus-container19767 \
  --name testfile.txt \
  --file /home/azureuser/testfile.txt
```

Replace `<ACCOUNT_KEY>` with the key obtained in Step 6.

#### Explanation

Uploads the file from the VM to Azure Blob Storage.

---

# Step 11: Verify Blob Upload

Run:

```bash
az storage blob list \
  --account-name nautilusstor19767 \
  --account-key $ACCOUNT_KEY \
  --container-name nautilus-container19767 \
  --output table
```

Expected Output:

| Name |
|------|
| testfile.txt |

#### Explanation

Confirms the file was uploaded successfully.

---

# Final Validation Checklist

✅ Existing Azure VM verified

✅ Storage Account created

✅ Private Blob Container created

✅ Storage Account access key retrieved

✅ Test file created on VM

✅ File uploaded successfully

✅ Blob verified in Azure Storage

✅ Task completed successfully

---

# Issues Faced During the Lab

| Issue | Resolution |
|---------|-----------|
| Azure CLI missing on the VM | Installed Azure CLI or used Cloud Shell where applicable |
| Authentication failed while uploading | Retrieved the correct Storage Account access key |
| Blob upload failed | Verified Storage Account and Container names |
| SSH connection issue | Checked NSG rules and VM public IP |
| Container not found | Created the Blob container before uploading |

---

# Best Practices

* Keep Blob Containers private unless public access is required.
* Store Storage Account keys securely.
* Prefer Azure AD authentication whenever possible.
* Verify uploads after every storage operation.
* Use Azure CLI for repeatable and automated deployments.

---

# Key Learnings

* Creating Azure Storage Accounts with LRS
* Managing private Blob Containers
* Retrieving Storage Account access keys
* Uploading files to Azure Blob Storage using Azure CLI
* Connecting Azure Virtual Machines with Azure Storage
* Verifying cloud storage operations using Azure CLI
* Understanding secure cloud storage workflows
