# Day 25 – Expanding and Managing Disk Storage

---

## Task Overview

The Nautilus DevOps Team needs to expand the storage capacity of an existing Azure Virtual Machine and attach a new managed data disk to support increased workloads.

For this task, perform the following operations:

| Task                 | Requirement                   |
| -------------------- | ----------------------------- |
| Expand Existing Disk | 32 GiB → 64 GiB               |
| Create New Disk      | `devops-disk`                 |
| Disk Size            | 64 GiB                        |
| Disk Type            | Standard HDD (`Standard_LRS`) |
| Mount Location       | `/mnt/devops-disk`            |

---

# Architecture Overview

```text id="n4k7p1"
devops-vm
   │
   ├── OS Disk (Expanded)
   │      32Gi → 64Gi
   │
   └── Data Disk
          devops-disk
               │
               ▼
      Mounted at:
      /mnt/devops-disk
```

---

# Step-by-Step Implementation Using Azure Portal

### Step 1: Login to Azure Portal

Open:

https://portal.azure.com

Login using your Azure credentials.

#### Explanation

Azure Portal is Microsoft's web-based interface for managing cloud resources.

---

### Step 2: Open Virtual Machine

Search:

| Search           |
| ---------------- |
| Virtual Machines |

Open the VM:

```text id="v7x2m4"
devops-vm
```

---

### Step 3: Stop the VM (Recommended)

Click:

```text id="m1k8p5"
Stop
```

Wait until the VM status becomes:

```text id="r5t9w2"
Stopped (deallocated)
```

#### Explanation

Stopping the VM ensures safe disk resizing operations.

---

### Step 4: Expand Existing OS Disk

Navigate:

| Path             |
| ---------------- |
| Settings → Disks |

Click the OS disk.

Select:

```text id="y3q7v1"
Size + performance
```

Change:

| Current Size | New Size |
| ------------ | -------- |
| 32 GiB       | 64 GiB   |

Click:

```text id="u6m4r8"
Save
```

#### Explanation

This increases the storage capacity of the Azure managed OS disk.

---

### Step 5: Start the VM

Go back to the VM Overview page.

Click:

```text id="q8p1x6"
Start
```

#### Explanation

Starts the VM after disk expansion.

---

### Step 6: Create New Managed Disk

Search:

| Search |
| ------ |
| Disks  |

Click:

```text id="t4v7m9"
+ Create
```

Configure:

| Setting     | Value                         |
| ----------- | ----------------------------- |
| Disk Name   | `devops-disk`                 |
| Region      | Same as VM                    |
| Disk Size   | 64 GiB                        |
| Performance | Standard HDD (`Standard_LRS`) |

Click:

* **Review + Create**
* **Create**

#### Explanation

Creates a new Azure managed data disk.

---

### Step 7: Attach Disk to VM

Go back to:

```text id="k2r5n8"
devops-vm
```

Navigate:

| Path             |
| ---------------- |
| Settings → Disks |

Click:

```text id="c7m1p4"
Attach existing disks
```

Select:

```text id="z5x8q2"
devops-disk
```

Click:

```text id="j9w3t6"
Save
```

#### Explanation

Attaches the new managed disk to the VM.

---

# Configure Disk Inside Linux VM

### Step 8: SSH into VM

Run:

```bash id="x1m7r5"
ssh azureuser@PUBLIC_IP
```

#### Explanation

Connects to the Azure Virtual Machine.

---

### Step 9: Check Available Disks

Run:

```bash id="p4t8v2"
lsblk
```

Expected Output:

```text id="b6n3q9"
sda
sdb
```

#### Explanation

Displays all block storage devices attached to the VM.

---

### Step 10: Create Filesystem

Run:

```bash id="w2k5m8"
sudo mkfs.ext4 /dev/sdb
```

#### Explanation

Formats the new disk using the ext4 filesystem.

---

### Step 11: Create Mount Directory

Run:

```bash id="g7r1x4"
sudo mkdir -p /mnt/devops-disk
```

#### Explanation

Creates the mount point directory.

---

### Step 12: Mount Disk

Run:

```bash id="t9v6m2"
sudo mount /dev/sdb1 /mnt/devops-disk
```

#### Explanation

Mounts the disk to the required directory.

---

### Step 13: Verify Disk Mount

Run:

```bash id="q3m8w7"
df -h
```

Expected Output:

```text id="r1x5p9"
/dev/sdb1 mounted on /mnt/devops-disk
```

#### Explanation

Confirms the disk is mounted successfully.

---

# Method 2: Using Azure CLI

### Step 1: Login to Azure

Run:

```bash id="f2p7m4"
az login
```

---

### Step 2: Find Resource Group

Run:

```bash id="m6v2x8"
az group list --output table
```

Store Resource Group:

```bash id="u4k9r1"
RG_NAME=$(az group list --query '[0].name' -o tsv)
```

---

### Step 3: Get Existing OS Disk Name

Run:

```bash id="z8q5t2"
OS_DISK=$(az vm show \
-g $RG_NAME \
-n devops-vm \
--query storageProfile.osDisk.name \
-o tsv)
```

#### Explanation

Retrieves the name of the VM OS disk.

---

### Step 4: Expand OS Disk

Run:

```bash id="n5m1w7"
az disk update \
  --resource-group $RG_NAME \
  --name $OS_DISK \
  --size-gb 64
```

#### Explanation

Expands the OS disk from 32 GiB to 64 GiB.

---

### Step 5: Create Data Disk

Run:

```bash id="c2x8p4"
az disk create \
  --resource-group $RG_NAME \
  --name devops-disk \
  --size-gb 64 \
  --sku Standard_LRS
```

#### Explanation

Creates a Standard HDD managed disk.

---

### Step 6: Attach Disk to VM

Run:

```bash id="j7v3m9"
az vm disk attach \
  --resource-group $RG_NAME \
  --vm-name devops-vm \
  --name devops-disk
```

#### Explanation

Attaches the new disk to the VM.

---

### Step 7: SSH into VM

Run:

```bash id="q6t1x5"
ssh azureuser@PUBLIC_IP
```

---

### Step 8: Configure Disk Inside VM

Run:

```bash id="p8r4w2"
sudo mkfs.ext4 /dev/sdb

sudo mkdir -p /mnt/devops-disk

sudo mount /dev/sdb1 /mnt/devops-disk
```

#### Explanation

Formats and mounts the attached disk.

---

### Step 9: Verify Disk Mount

Run:

```bash id="x4m7p1"
df -h
```

Expected Output:

```text id="n9v2r6"
/dev/sdb1 mounted on /mnt/devops-disk
```

---

# Quick Commands Only (Fastest Solution)

```bash id="m1x8q4"
RG_NAME=$(az group list --query '[0].name' -o tsv)

OS_DISK=$(az vm show \
-g $RG_NAME \
-n devops-vm \
--query storageProfile.osDisk.name \
-o tsv)

az disk update \
--resource-group $RG_NAME \
--name $OS_DISK \
--size-gb 64

az disk create \
--resource-group $RG_NAME \
--name devops-disk \
--size-gb 64 \
--sku Standard_LRS

az vm disk attach \
--resource-group $RG_NAME \
--vm-name devops-vm \
--name devops-disk
```

Inside VM:

```bash id="w7p2m5"
sudo umount /mnt

sudo mkdir -p /mnt/devops-disk

sudo mount /dev/sdb1 /mnt/devops-disk

df -h
```

---

# Best Practices

* Use managed disks for better scalability
* Monitor storage utilization regularly
* Use Standard HDD for cost-effective workloads
* Mount data disks separately from OS disks
* Validate disk mounts after configuration

---

# Key Learnings

* Azure Managed Disks simplify storage management
* OS disks can be resized dynamically
* Data disks provide additional application storage
* Linux filesystems must be formatted before mounting
* Disk mounting is a critical Linux administration skill
* Storage scalability is essential in cloud environments
