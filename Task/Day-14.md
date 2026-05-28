# Day 14 – Create and Attach Managed Disks in Azure

---

## Task Overview

The Nautilus DevOps team is strategizing the migration of a portion of their infrastructure to the Azure cloud. Recognizing the scale of this undertaking, they have opted to approach the migration in incremental steps rather than as a single massive transition. To achieve this, they have segmented large tasks into smaller, more manageable units. This granular approach enables the team to execute the migration in gradual phases, ensuring smoother implementation and minimizing disruption to ongoing operations.

For this task, create a Managed Disk with the following requirements:

* Disk Name: `xfusion-disk`
* Disk Type: `Standard_LRS`
* Disk Size: `2 GiB`

---

# Step-by-Step Implementation (Azure Portal UI)

### Step 1: Login to Azure Portal

Open the Azure Portal:

https://portal.azure.com

Login using your Azure account credentials.

#### Explanation

The Azure Portal is Microsoft Azure's web-based management interface used to create and manage cloud resources.

---

### Step 2: Navigate to Disks

Use the top search bar and search for:

| Search |
| ------ |
| Disks  |

Open the **Disks** service from the results.

#### Explanation

The Disks service is used to create and manage Azure Managed Disks.

---

### Step 3: Create a New Managed Disk

Click:

* **+ Create**

#### Explanation

This starts the process of creating a new Managed Disk resource.

---

### Step 4: Configure Basic Settings

Under **Basics**, configure the following:

| Setting        | Value                             |
| -------------- | --------------------------------- |
| Subscription   | Default Azure Subscription        |
| Resource Group | Existing Resource Group           |
| Disk Name      | `xfusion-disk`                    |
| Region         | Same region as existing resources |

#### Explanation

These settings define where the Managed Disk will be created and how it will be identified.

---

### Step 5: Configure Disk Settings

Under **Performance + Size**, configure:

| Setting      | Value                       |
| ------------ | --------------------------- |
| Storage Type | Standard HDD (Standard_LRS) |
| Disk Size    | 2 GiB                       |

#### Explanation

Standard_LRS provides cost-effective locally redundant storage suitable for development and testing workloads.

---

### Step 6: Review and Create

Click:

* **Review + Create**
* **Create**

#### Explanation

Azure validates the configuration and provisions the Managed Disk.

---

### Step 7: Verify Disk Creation

Navigate back to:

| Service |
| ------- |
| Disks   |

Verify the following:

| Property  | Expected Value |
| --------- | -------------- |
| Name      | xfusion-disk   |
| Disk Size | 2 GiB          |
| Disk Type | Standard_LRS   |

#### Explanation

This confirms that the Managed Disk was successfully created.

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

This command lists all available Resource Groups in the Azure subscription.

---

### Step 3: Set Resource Group Variable

Run:

```bash
RG_NAME="kml_rg_main-145620288f634136"
```

#### Explanation

This variable stores the Resource Group name for easier command execution.

---

### Step 4: Create Managed Disk

Run:

```bash
az disk create \
  --resource-group $RG_NAME \
  --name xfusion-disk \
  --sku Standard_LRS \
  --size-gb 2
```

#### Explanation

This command creates a Managed Disk named `xfusion-disk` with:

* Storage Type: Standard_LRS
* Disk Size: 2 GiB

---

### Step 5: Verify Managed Disk

Run:

```bash
az disk show \
  --resource-group $RG_NAME \
  --name xfusion-disk \
  --output table
```

Expected Output:

| Name         | DiskSizeGb | Sku          |
| ------------ | ---------- | ------------ |
| xfusion-disk | 2          | Standard_LRS |

#### Explanation

This command displays the disk details and confirms successful creation.

---

# Best Practices

* Use meaningful naming conventions for disks
* Select storage types based on workload requirements
* Monitor disk usage regularly
* Use Managed Disks for simplified storage management
* Apply tags for resource organization and governance

---

# Key Learnings

* Managed Disks provide persistent storage for Azure workloads
* Standard_LRS offers cost-effective locally redundant storage
* Azure automatically manages disk availability and durability
* Managed Disks simplify storage administration
* Azure CLI enables efficient storage provisioning and management
