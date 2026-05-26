# Day 12 – Add and Manage Tags for Azure Virtual Machines

---

## Task Overview

The Nautilus DevOps team is migrating a portion of their infrastructure to Microsoft Azure. During the migration process, several Virtual Machines have been created across different regions. The team identified one Virtual Machine that was not tagged properly and decided to add the required tag for better resource management and governance.

For this task:

* An existing Virtual Machine named `nautilus-vm` already exists

Your objective is to:

* Add the tag `Environment=dev`
* Ensure the tag is successfully applied to the Virtual Machine

---

# Step-by-Step Implementation (Azure Portal UI)

### Step 1: Login to Azure Portal

Open the Azure Portal:

https://portal.azure.com

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

The Virtual Machines service is used to manage Azure compute resources and configurations.

---

### Step 3: Open Existing Virtual Machine

Select the Virtual Machine:

| VM Name       |
| ------------- |
| `nautilus-vm` |

#### Explanation

This opens the configuration page of the existing Azure Virtual Machine.

---

### Step 4: Open Tags Section

Inside the Virtual Machine menu:

Navigate to:

| Section         |
| --------------- |
| Settings → Tags |

#### Explanation

The Tags section is used to organize and categorize Azure resources using key-value pairs.

---

### Step 5: Add Tag

Configure the following:

| Name        | Value |
| ----------- | ----- |
| Environment | dev   |

#### Explanation

This tag identifies the Virtual Machine as part of the Development environment.

---

### Step 6: Save Configuration

Click:

* **Apply**
* **Save**

#### Explanation

Azure updates the resource metadata and stores the tag with the Virtual Machine.

---

### Step 7: Verify Tag

Verify the following:

| Tag Name    | Tag Value |
| ----------- | --------- |
| Environment | dev       |

#### Explanation

This confirms that the tag was successfully added to the Virtual Machine.

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

### Step 2: Set Resource Group Variable

Run:

```bash
RG_NAME="kml_rg_main-145620288f634136"
```

#### Explanation

This variable stores the Resource Group name for easier command execution.

---

### Step 3: Add Tag to Virtual Machine

Run:

```bash
az vm update \
  --resource-group $RG_NAME \
  --name nautilus-vm \
  --set tags.Environment=dev
```

#### Explanation

This command adds the tag `Environment=dev` to the Virtual Machine.

---

### Step 4: Verify Tag

Run:

```bash
az vm show \
  --resource-group $RG_NAME \
  --name nautilus-vm \
  --query tags
```

Expected Output:

```json
{
  "Environment": "dev"
}
```

#### Explanation

This command displays all tags attached to the Virtual Machine.

---

# Best Practices

* Use consistent tag naming conventions
* Apply tags to all Azure resources
* Use tags for cost allocation and governance
* Separate environments using tags
* Automate tagging policies whenever possible

---

# Key Learnings

* Tags are key-value pairs used to organize Azure resources
* Tags simplify resource management and governance
* Azure tags help with cost tracking and reporting
* Tags are widely used in enterprise cloud environments
* Azure CLI enables automated resource tagging
