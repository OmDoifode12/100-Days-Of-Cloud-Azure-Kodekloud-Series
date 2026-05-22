# Day 08 – Attach Managed Disk to Azure Virtual Machine

---

## Task Overview

The Nautilus DevOps team is migrating services to Microsoft Azure in incremental phases to ensure better control, optimization, and minimal disruption during migration activities.

For this task:

* An existing Virtual Machine named `devops-vm` already exists
* An existing Managed Disk named `devops-disk` already exists
* Both resources are located in the `westus` region

Your objective is to:

* Attach the managed disk `devops-disk` to the Virtual Machine `devops-vm`
* Ensure the disk is attached as a **Data Disk**
* Ensure the Virtual Machine initialization is fully completed before submission

---

# Step-by-Step Implementation (Azure Portal UI)

### Step 1: Login to Azure Portal

Open the Azure Portal:

[Microsoft Azure Portal](https://portal.azure.com?utm_source=chatgpt.com)

Login using your Azure account credentials.

#### Explanation

The Azure Portal is Microsoft Azure’s web-based management interface used to create and manage cloud resources.

---

### Step 2: Navigate to Virtual Machines

Use the top search bar and search for:

| Search           |
| ---------------- |
| Virtual Machines |

Open the **Virtual Machines** service from the results.

#### Explanation

The Virtual Machines service is used to manage Azure compute resources and their configurations.

---

### Step 3: Open Existing Virtual Machine

Select the Virtual Machine:

| VM Name     |
| ----------- |
| `devops-vm` |

#### Explanation

This opens the configuration page of the existing Azure Virtual Machine.

---

### Step 4: Verify VM Status

Ensure the Virtual Machine status is:

| Property | Expected Value |
| -------- | -------------- |
| Status   | Running        |

#### Important

Wait for VM initialization to complete fully before proceeding.

#### Explanation

Azure services and VM agents must finish initialization to ensure stable disk attachment operations.

---

### Step 5: Open Disks Section

Inside the Virtual Machine menu:

Navigate to:

| Section          |
| ---------------- |
| Settings → Disks |

#### Explanation

The Disks section is used to manage OS disks and additional Data Disks attached to the Virtual Machine.

---

### Step 6: Attach Existing Managed Disk

Under the **Data disks** section:

Click:

* **Attach existing disks**

Select the following disk:

| Disk Name     |
| ------------- |
| `devops-disk` |

#### Explanation

This operation attaches the existing managed disk to the Virtual Machine as a Data Disk.

---

### Step 7: Save Configuration

Click:

* **Save**

Wait for the deployment process to complete successfully.

#### Explanation

Azure updates the Virtual Machine configuration and attaches the managed disk.

---

### Step 8: Verify Disk Attachment

After deployment completes, verify the following:

| Property  | Expected Value |
| --------- | -------------- |
| Data Disk | `devops-disk`  |

#### Explanation

This confirms that the managed disk has been successfully attached to the Virtual Machine.

---

# Method 2: Using Azure CLI

### Step 1: Login to Azure

Run:

```bash id="n0fr3w"
az login
```

#### Explanation

This command authenticates Azure CLI with your Azure account.

---

### Step 2: Check Available Resource Groups

Run:

```bash id="m5ur2q"
az group list --output table
```

#### Explanation

This command lists all available Resource Groups in the Azure subscription.

---

### Step 3: Set Resource Group Variable

Run:

```bash id="rvjlwm"
RG_NAME="kml_rg_main-145620288f634136"
```

#### Explanation

This variable stores the Resource Group name for easier command execution.

---

### Step 4: Attach Managed Disk to VM

Run the following command:

```bash id="5jlwm9"
az vm disk attach \
  --resource-group $RG_NAME \
  --vm-name devops-vm \
  --name devops-disk
```

#### Explanation

This command attaches the managed disk `devops-disk` to the Virtual Machine `devops-vm` as a Data Disk.

---

### Step 5: Verify Disk Attachment

Run:

```bash id="jlwm7a"
az vm show \
  --resource-group $RG_NAME \
  --name devops-vm \
  --query "storageProfile.dataDisks" \
  --output table
```

#### Explanation

This command displays all data disks attached to the Virtual Machine.

Expected Output:

| Name        |
| ----------- |
| devops-disk |

---

# Best Practices

* Use Managed Disks for production workloads
* Separate application data from OS disks
* Regularly monitor disk performance and utilization
* Apply backup policies for critical data disks
* Use proper naming conventions for disks

---

# Key Learnings

* Managed Disks provide scalable and persistent cloud storage
* Data Disks are used for application and workload storage
* Azure allows disks to be attached dynamically to Virtual Machines
* Azure CLI simplifies infrastructure and storage management
* Proper disk management is critical for production cloud environments
